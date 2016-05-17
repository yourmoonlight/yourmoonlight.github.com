---
layout: post
title: DECO让Python并发变得超级简单
category: learning
tags: [翻译, Python, Concurrency]
---

最近发现了个好玩的库deco，对Python multiprocessing进行了封装，由美国威斯康星大学的Alex Sherman和 Peter Den Hartog共同开发，对应的论文地址[paper](https://drive.google.com/a/mozilla.com/file/d/0B_olmC0u8E3gWTBmN3pydGxHdEE/view)。

[deco]( https://github.com/alex-sherman/deco)使用装饰器，让Python并发变得超级简单，有一点需要说明的是，如果你要收集子进程的执行结果，那你需要传一个基于key索引的mutable对象（比如，dict）。Python list 也是mutable，但是不是基于key索引，所以传进去，会引起竞争。

我们来看个简单的小例子：

```python
# before.py

def slow(index):
    time.sleep(5) # 模拟耗时操作

def run():
    for index in list('123'):
        slow(index)
        
run()
```

运行上面这个例子，会发现理所应当的耗时15秒：

```python
$ time python before.py

python before.py  0.02s user 0.01s system 0% cpu 15.048 total
```

现在我们装上deco，pip install deco，

```python
# after.py

from deco import concurrent, synchronized

@concurrent
def slow(index):
    time.sleep(5)

@synchronized
def run():
    for index in list('123'):
        slow(index)
run()
```

这次我们运行after.py，肯定是少于15秒的：

```python
$ time python after.py

python after.py  0.09s user 0.04s system 2% cpu 5.242 total
```

我们简单加点log 看看它的执行顺序：

```python
def slow(index):
    time.sleep(5)
    print('done with {}'.format(index))
```

然后运行两次，看一下：

```python
$ python after.py
done with 1
done with 3
done with 2

$ python after.py
done with 3
done with 1
done with 2
```

同时有几个进程并发的，是由multiprocessing.Pool决定的，对应到deco，可以用concurrent里的processes参数设置。

```python
from deco import concurrent

@concurrent(processes=5)
def slow_func():
    ...
```

这里的process数量最好等于cpu核数。
原文[地址](https://www.peterbe.com/plog/deco)。



