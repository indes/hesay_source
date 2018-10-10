---
title: "🐍Python 装饰器学习"
date: 2018-10-10T10:29:52+08:00
draft: false
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

我们先看看 Python 装饰器是干什么的，首先我们先定义一个这样的函数：

```python
def deco(func):
    '''
    该函数参数为另外一个函数
    '''
	def inner():
		print('running %s' % func.__name__)
		return func()
	return inner
```
这里的 deco 函数其实就是一个装饰器，将函数作为参数传入，就能使用该装饰器。以下两段代码是等价的，输出的结果一样。

```python
def func1():
    print('running func1()')

func1 = deco(func1)
func1()
```

```python
@deco
def func2():
	print('running func2()')

func2()
```

从上面哪个例子可以发现，Python 装饰器其实就是语法糖。正是因为这样所以使用装饰器的时候只要在被装饰函数上面一行使用 @ 符号 + 装饰器就可以了，可以省去一大段代码。

## 实现一个简单的装饰器

了解了什么是装饰器后，我们来实习一个简单的装饰器。该装饰器可以打印被装饰的函数的运行时间、传入参数和运行结果。

```python
import time

def timethis(func):
    def wrapper(*args):
        start = time.time()
        result = func(*args)
        end = time.time()
        args_str = ', '.join(repr(arg) for arg in args)
        print('[%0.8fs] %s(%s) -> %r' %(end-start, func.__name__, args_str, result))
        return result
    return wrapper
```

这里给出两个使用该装饰器的示例，求累加与求第 n 个斐波那契数。

```python
@timethis
def accumulate(end):
    count = 0
    for i in range(end):
        count = count + i
    return count

accumulate(100)
```

```python
@timethis
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-2) + fibonacci(n-1)

fibonacci(6)
```

### 保留函数元信息
使用装饰器时，原函数的元信息都会丢失，这个时候我们就需要使用 `functools` 库中的 `@wraps` 装饰器来注解底层包装函数。建议在定义自己的装饰器的时候都加上这个装饰器。

比如，之前我们定义的装饰器就要修改为这样：

```python
import time
from functools import wraps

def timethis2(func):
    @wraps(func)
    def wrapper(*args):
        start = time.time()
        result = func(*args)
        end = time.time()
        args_str = ', '.join(repr(arg) for arg in args)
        print('[%0.8fs] %s(%s) -> %r' %(end-start, func.__name__, args_str, result))
        return result
    return wrapper
```

```python
# 使用了 @wraps 的装饰器 
@timethis2
def accumulate2(end):
    count = 0
    for i in range(end):
        count = count + i
    return count

accumulate2.__name__, accumulate.__name__
```

从上面的输出可以看出使用了 `@wraps` 装饰器定义的装饰器保留了原函数的元信息。


## 解除装饰器

有时候需要使用未被包装的函数，可以使用`__wrapped__`属性（只有使用了`@wraps`定义的装饰器才有这个属性）

```python
accumulate2.__wrapped__(6)
```
未使用 `@wrapped` 定义的装饰器所装饰的函数没有该属性。


## 带参数的装饰器

如果想给装饰器添加参数，可以参考下面的例子。

这里我们还是定义一个打印运行时间的装饰器，这里我们定义的时候使用了一个参数 display_result, 当该参数为 False 时不打印函数运行结果。

```python
def timethis3(display_result=True):
    def decorate(func):
        @wraps(func)
        def wrapper(*args):
            start = time.time()
            result = func(*args)
            end = time.time()
            args_str = ', '.join(repr(arg) for arg in args)
            if(display_result):
                print('[%0.8fs] %s(%s) -> %r' %(end-start, func.__name__, args_str, result))
            else:
                print('[%0.8fs] %s(%s)' %(end-start, func.__name__, args_str))
            
            return result
        return wrapper
    return decorate
```

在定义被装饰的函数的时候，可以在装饰器后面添加参数。
```python
@timethis3(display_result=False)
def accumulate3(end):
    count = 0
    for i in range(end):
        count = count + i
    return count

accumulate3(100)
```

```python
@timethis3(display_result=True)
def accumulate4(end):
    count = 0
    for i in range(end):
        count = count + i
    return count

accumulate4(100)
```

## 装饰器叠加

有时候需要使用到多个装饰器，这个时候该如何定义呢？
直接按顺序将装饰器放在函数上面就行了。

```python
@d1
@d2
def f():
    pass
```

等价于以下代码：

```python
def f()
    pass

f = d1(d2(f))
```