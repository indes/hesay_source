---
title: "⚗️Python 装饰器学习"
date: 2018-10-10T10:29:52+08:00
draft: true
categories:
- Code
tags:
- Python
- 装饰器
---
最近在折腾 Telegram Bot 时使用到了 Python 装饰器，顺便记录一下。

<!--more-->

## 什么是装饰器
装饰器是一个可调用的对象，参数为另一个被装饰的函数，返回一个可调用的对象或函数。

```python
@decorate
def func():
    print('running func()')
```

```python
def func():
    print('running func()')

target = decorate(func)
```

