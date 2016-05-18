---
layout: post
title: 用Django的Signal方便地把自定义日志存到数据库
category: learning
tags: [Django, Signal, Logging]
---

有时候需要根据业务，制定log系统，比如需要把一个对象的状态变换给记录下来，留着以后分析。刚看了篇文章，可以用django的signal来做自定义日志记录。

让我们先来看看一个可能的应用场景，比如我们的应用使用有限状态机来处理用户的状态变换，可以是invited，active，locked 或者deactivated。从一个状态变换到另一个状态，有可能是用户自己触发的，比如输错太多次密码，也有可能是从管理后台由管理员触发的。

我们想做的事，就是把每一次用户状态的变化，包括是谁发起的，受影响的是谁，原状态，变化后的状态，都记录到数据库里。

首先建个记录状态变化的model：

```python
from django.db import models

class UserStateAudit(models.Model):
    username = models.TextField(null=False, blank=False)
    initiated_by = models.TextField(null=False, blank=False)
    start_state = models.TextField(null=False, blank=False)
    end_state = models.TextField(null=False, blank=False)
    datetime = models.DateTimeField(auto_now_add=True, null=True, blank=True)
    ip_address = models.TextField()
```

这个表记录用户的哪些状态，一目了然。

现在我们建个signal，用来发送想要记下的东西，状态改变的用户，改变之前的状态，改变之后的状态。在signals.py里，我们添加：

```python
from django.dispatch import Signal

state_audit_signal = Signal(providing_args=['user', 'old_state', 'new_state'])
```

比如，你的用户有限状体机model叫FSM，你可以定义这个FSM的一个实例方法state_change，一有状态变换，就在这个实例方法里发送state_audit_signal，具体操作如下：

```python
from play_signal.signals import state_audit_signal

class UserFSM(models.Model):
    user = models.OneToOneField(User)
    current_state = models.CharField(null=False, blank=False, max_length=32)
    [.....]
    def state_change(self, e):
        state_audit_signal.send(
            sender=self.__class__,
            user=self.user,
            old_state=e['src'],
            new_state=e['dst']
            )
        self.current_state = e['dst']
        self.save()
```

好了，现在信号可以发了，在哪接收呢？我们在signals_processing.py里添加处理信号的逻辑：

```python
from django.dispatch import receiver
from crequest.middleware import CrequestMiddleware
from play_signals.models import UserStateAudit, User
from play_signals.signals import state_audit_signal
 
 
@receiver(state_audit_signal)
def user_change_state_signal(sender, **kwargs):
    current_request = CrequestMiddleware.get_request()
    user_id = kwargs['user']
    old_state = kwargs['old_state']
    new_state = kwargs['new_state']
 
    user = User.objects.get(pk=user_id)
 
    username = user.username
    initiated_by = current_request.user.username if current_request else 'CLI'
    start_state = old_state
    end_state = new_state
    ip = get_client_ip(current_request) if current_request else 'CLI'
 
    audit = UserStateAudit.objects.create(
        username=username,
        initiated_by=initiated_by,
        start_state=start_state,
        end_state=end_state,
        ip_address=ip
    )
 
    audit.save()

```

这里我们用[Django Crequest](https://pypi.python.org/pypi/django-crequest)来获取是谁发起的请求。

django的signal就是这么方便。

[原文链接](https://www.algotech.solutions/blog/python/using-django-signals-for-database-logging/)



