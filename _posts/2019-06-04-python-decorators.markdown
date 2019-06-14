---
layout: post
permalink: "/decorators-in-python"
title:  "Базовое использование декораторов в Python"
date:   2019-06-04 21:01:16 +0300
categories: python
---
Декораторы - это вызываемые объекты, функции, которые принимают в качестве аргумента другой вызываемый объект, 
и внутри себя объявляют функцию-обертку, и возвращают ее. Таким образом, функция-обертка
перехватывает вызовы декорированной функции и выполняет дополнительные действия.

На самом деле, это достаточно сложная тема, но здесь я рассмотрю их базовое использование.
Наверное, так выглядит более наглядно:
```python
def decorator(func):
    def wrapper():
        print("I'm a decorator.")
        func()
    return wrapper
```
Так как в Python функция - это объект, то можно внутри функций объявлять еще функции, можно присвоить 
объект функции переменной, потом вызывать ее через новую переменную.
```python
def function():
    print("I'm a function!")


not_a_function = function
not_a_function()

# I'm a function!
```
С помощью декоратора можно изменить поведение передаваемой функции, при этом никак не меняя саму функцию. 
Можно сделать что-то до, что-то после вызова декорируемой функции, а можно в принципе не вызывать 
эту самую функцию - но это, конечно, мягко говоря, не самое очевидное поведение, и посему не рекомендуется.
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
Последний блок кода можно переписать с использованием синтаксиса декораторов, эффект будет тот же:
```python
@decorator
def my_function():
    print("I'm just a regular function.") 

my_function()
# Вызов декорированной функции выведет
# I'm a decorator.
# I'm just a regular function.
```
Есть небольшая проблема в связи с этим, например, если мы попытаемся взять атрибут `__name__` у декорированной 
функции, увидим нечто такое:
```python
print(my_function.__name__)
# wrapper
``` 
Чтобы получить ее настоящее название, можно воспользоваться методом wraps из встроенной библиотеки functools:
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
Декораторы можно применять также и для методов классов, т.к. методы класса, это те же функции, только 
принимающие в качестве первого аргумента `self` объект класса.

```python
def awesome_decorator(func):
    def wrapper(self):
        self.name = "Gilfoyle"
        func(self)
    return wrapper
    
class Developer:
    def __init__(self, name="Dinesh"):
        self.name = name
    
    @awesome_decorator
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
Самому декоратору передается объект функции/метода, а аргументы, с которыми вызывается уже 
декорированная функция - внутренней функции-обертке.

Попробую привести примеры, когда это используется на практике.

Самые часто используемые встроенные декораторы в Python - это декораторы методов классов `classmethod`, 
`staticmethod`, `property`.
```python
class SomeClass:
    name = "SomeClass"

    @classmethod
    def get_class_attribute(cls):
        print(cls.name)
    
    @staticmethod
    def do_something(action):
        print(action)
SomeClass.get_class_attribute()
SomeClass.do_something("dance")
```
Методы, декорированные `classmethod` и `staticmethod` соответственно, отличаются только передаваемыми в 
них аргументами.
`classmethod` получает первым аргументом объект класса 
(когда как обычный метод класса получает экземпляр класса `self`).
`staticmethod` в качестве аргументов получает только те аргументы, которые ему напрямую передаются при вызове, 
и не имеет доступа к классу/экземпляру класса. Оба могут вызываться как с созданием, 
так и без создания экземпляра класса.

`property` декоратор может быть использован, например, для создания абстрактного атрибута у класса, 
в связке с еще одним декоратором, `abstractmethod`.
```python
from abc import ABC, abstractmethod


class BaseClass(ABC):
    @property
    @abstractmethod
    def my_abstract_property(self):
        pass


class MyClass(BaseClass):
    pass

# Выведет:
# TypeError: Can't instantiate abstract class MyClass with abstract methods my_abstract_property
```
Для тестов часто используется еще декоратор `patch` из библиотеки 
[unittest.mock](https://docs.python.org/3/library/unittest.mock.html).
Он позволяет заменять некоторые части программы на "ненастоящие", mock-объекты.
Его можно использовать следующим образом:
```python
from unittest.mock import patch


class Class:
    def method(self):
        pass
        
class MockClass:
    def method(self):
        return "I'm a mock class!"


@patch("__main__.Class", new=MockClass)
def test_something():
    instance = Class()
    assert instance.method() == "I'm a mock class!"
    assert isinstance(instance, MockClass)
test_something()
# Не будет никакого исключения
```
Внутри декорируемой функции `patch` заменяет объект, переданный ему в качестве первого аргумента
(это должен быть импортируемый по этому пути из текущего окружения объект), на объект, 
переданный в качестве ключевого аргумента `new`. Это самое простое его применение, у этой библиотеки
очень много возможностей, можно почитать ее документацию.
