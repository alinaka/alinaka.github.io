---
layout: post
permalink: "/decorators-in-python"
title:  "Введение в декораторы"
date:   2019-06-03 16:37:16 +0300
categories: python
---
Декораторы - это одна из тех вещей, которые просто необходимо знать. 
По сути, это просто функции-обертки, т.е. функция, которая принимает в качестве аргумента другую функцию. Она выглядит примерно так:
```python
def decorator(func):
    def wrapper():
        print("I'm a decorator.")
        func()
    return wrapper
```
С помощью декоратора можно изменить поведение передаваемой функции, при этом никак не меняя саму фукнцию. Можно сделать что-то до, что-то после вызова декорируемой функции, а можно в принципе не вызывать эту самую функцию - но это, конечно, не самое очевидное поведение, и посему не рекомендуется.
Например:
    
```python
def my_function():
    print("I'm just a regular function.") 
     
# Таким образом происходит декорирование функции
my_function = decorator(my_function)

my_function()
# Вызов декорированной функции выведет
# I'm a decorator.
# I'm just a regular function.
```
Последний блок кода можно переписать таким образом, эффект будет тот же:
```python
@decorator
def my_function():
    print("I'm just a regular function.") 

my_function()
# Вызов декорированной функции выведет
# I'm a decorator.
# I'm just a regular function.
```
Есть небольшая проблема в связи с этим, например, если мы попытаемся взять атрибут `__name__` у декорированной функции, увидим нечто такое:
```python
print(my_function.__name__)
# wrapper
``` 
Чтобы получить ее название, можно сделать воспользоваться методом wraps из встроенным библиотеки functools
```python
from functools import wraps


def decorator(func):
    @wraps(func)
    def wrapper():
        print("I'm a decorator.")
        func()
    return wrapper
    
    
@decorator
def my_function():
    print("I'm just a regular function.") 
    

print(my_function.__name__)
# my_function
``` 
Декораторы можно применять также и для методов классов, т.к. методы класса, это те же функции, только принимающие как первый аргумент `self` объекты класса.

```python
def hello_decorator(func):
    def wrapper(self):
        self.name = "Gilfoyle"
        func(self)
    return wrapper
    
class Developer:
    def __init__(self, name="Dinesh"):
        self.name = name
    
    @hello_decorator
    def say_name(self):
        print(f"My name is {self.name}")
        
        
dev = Developer()
dev.say_name()

# My name is Gilfoyle
```
Ну и конечно, передача именованных и неименованных аргументов.
```python
def decorator_with_args(func):
    def wrapper(*args, **kwargs):
        print("I got these args and kwargs", args, kwargs)
        func(*args, **kwargs)
    return wrapper


@decorator_with_args
def decorated_function_with_args(first_name, last_name, alias="Random string"):
    print(f"{first_name} {last_name} is {alias}")
    
    
decorated_function_with_args("Nelson", "Bighetti", alias="Big Head")

# I got these args and kwargs ('Nelson', 'Bighetti') {'alias': 'Big Head'}
# Nelson Bighetti is Big Head
```
То есть видно, что самому декоратору передается объект функции/метода, а аргументы, с ккоторыми уже декорированная функция вызывается - внутренней функции-обертке.
