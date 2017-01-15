---
layout: post
title: 分享使用redigo连到redis proxy踩到的坑
category: Golang
tags: [Golang,redigo,redis proxy]
---



最近在开发项目的时候，踩了一个redis相关的坑，现分享给大家。

使用的第三方库是 redigo，

连的redis地址是一个proxy，

首先要说的是，redigo 和 redis proxy 都是好东西，redigo的源码看着很清晰，redis proxy也很棒。刀是好刀，但是用的姿势不对就会伤到自己。

这个坑就是关于redigo连接池的：

```go
RedisPool = &redis.Pool{
		MaxIdle:     5,
		IdleTimeout: 240 * time.Second,
		Dial: func() (redis.Conn, error) {
			c, err := redis.Dial("tcp", address)
			if err != nil {
				println(err)
				return nil, err
			}
			_, err = c.Do("SELECT", db)
			if err != nil {
				println(err)
				return nil, err
			}
			return c, nil
		},
		TestOnBorrow: func(c redis.Conn, t time.Time) error {
			_, err := c.Do("PING")
			if err != nil {
				println(err)
			}
			return err

		},
	}
```



上图所示为老项目中的redigo redis pool，咋一看没啥问题，

MaxIdle: 池子里的最大空闲连接

IdleTimeout: 超过这个duration的空闲连接，会被关闭

TestOnBorrow: 用之前检查这个连接是不是健康的

问题就在于这个Dial，

> Dial is an application supplied function for creating and configuring a connection. 

pool结构体里的这个Dial 就是用来创建，**并配置**一个redis连接的。

**坑就在这，**我们执行redis 命令是通过connection对象的Do方法，

```go
func (c *conn) Do(cmd string, args ...interface{}) (interface{}, error) {
	c.mu.Lock()
	pending := c.pending
	c.pending = 0
	c.mu.Unlock()

	if cmd == "" && pending == 0 {
		return nil, nil
	}

	if c.writeTimeout != 0 {
		c.conn.SetWriteDeadline(time.Now().Add(c.writeTimeout))
	}

	if cmd != "" {
		if err := c.writeCommand(cmd, args); err != nil {
			return nil, c.fatal(err)
		}
	}

	if err := c.bw.Flush(); err != nil {
		return nil, c.fatal(err)
	}

	if c.readTimeout != 0 {
		c.conn.SetReadDeadline(time.Now().Add(c.readTimeout))
	}

	if cmd == "" {
		reply := make([]interface{}, pending)
		for i := range reply {
			r, e := c.readReply()
			if e != nil {
				return nil, c.fatal(e)
			}
			reply[i] = r
		}
		return reply, nil
	}

	var err error
	var reply interface{}
	for i := 0; i <= pending; i++ {
		var e error
		if reply, e = c.readReply(); e != nil {
			return nil, c.fatal(e)
		}
		if e, ok := reply.(Error); ok && err == nil {
			err = e
		}
	}
	return reply, err
}
```



如上述源码中，如果你不传入timeout的值，那么默认0值的话，这两个set deadline的逻辑就跳过了。。。

如果不设置read/write timeout 会导致什么问题呢？假如网络有波动，执行一个redis 命令的时候，一直没收到服务器的响应，会导致这次请求一直没有返回，晾在那。直到redis服务器设置的超时时间到了，关闭连接，然后就会读到一个EOF的错误。

1. 单点redis的情况，如果不设置MaxActive，redis pool的连接数是没有上限的，问题就不会暴露出来，这对我们的服务来说，影响也不大，就是在错误日志中，会多几条redis相关的EOF日志，但是这样真的没问题么？当然有问题，如果是从redis读消息，没有设置read timeout，一直读不到，这个协程就卡在那，迟迟不给响应，对用户来说很不好。
2. 使用集群模式，一般redis_proxy 会限制连接数，所以redis pool 就应该用MaxActive限制池子里的最大连接数，这时候如果不设置read/write timeout，问题就来了，池子里的连接会越来越少直到没有。

因此，不管那种情况，我们都应该给redis.Dial这个方法，传入三个超时时间，DialConnectTimeout， DialReadTimeout，DialWriteTimeout。

附上现在在用的 redigo的redis pool。

```go
redis.Pool{
			MaxIdle: cf.StatsRedis.MaxIdle,
			MaxActive: cf.StatsRedis.MaxActive,
			Dial: func() (redis.Conn, error) {
				c, err := redis.Dial("tcp", cf.StatsRedis.ProxyAddress,
					redis.DialConnectTimeout(time.Duration(cf.StatsRedis.ConnectTimeout) * time.Millisecond),
					redis.DialReadTimeout(time.Duration(cf.StatsRedis.ReadTimeout) * time.Millisecond),
					redis.DialWriteTimeout(time.Duration(cf.StatsRedis.WriteTimeout) * time.Millisecond))
				if err != nil {
					l4g.Error(err)
					return nil, err
				}
				return c, nil
			},
			// Use the TestOnBorrow function to check the health of an idle connection
			// before the connection is returned to the application.
			TestOnBorrow: func(c redis.Conn, t time.Time) error {
				if time.Since(t) < time.Minute {
					return nil
				}
				_, err := c.Do("PING")
				return err
			},
			IdleTimeout: 300 * time.Second,
			// If Wait is true and the pool is at the MaxActive limit,
			// then Get() waits for a connection to be returned to the pool before returning
			Wait: true,
		},
```

之前一直以为redis.Dial这个方法会有默认的超时时间，结果事实证明它没有啊。。

写代码一定不能用“以为”。

其实也不能怪redigo，这个库底层用的是golang的net库，default timeout 就是0啊，which is interpreted as “no timeout”...


