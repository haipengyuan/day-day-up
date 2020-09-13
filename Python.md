## Python：解释型语言

### 安装
在Windows上运行Python时，请先启动命令行，然后运行python。
在Mac和Linux上运行Python时，请打开终端，然后运行python3。

### 输入和输出
#### 输出
```python
>>> print('hello world')
hello world

>>> print('The quick brown fox', 'jumps over', 'the lazy dog')
The quick brown fox jumps over the lazy dog
```

#### 输入
输入任意字符，然后按回车后完成输入。
```python
>>> name = input()
>>> name = input('please enter your name: ')
```

### Python基础
#### 空值：None

#### 除法
`/ `除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数。

```python
>>> 9 / 3
3.0
```

`//`称为地板除，两个整数的除法仍然是整数。

```python
>>> 10 // 3
3
```

#### 编码
ASCII编码1个字节，Unicode编码2个字节。
UTF-8，可变长编码，英文字母被编码成1个字节，汉字通常是3个字节。

计算机系统通用的字符编码工作方式：
在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。
用记事本编辑的时候，从文件读取的UTF-8字符被转换为Unicode字符到内存里，编辑完成后，保存的时候再把Unicode转换为UTF-8保存到文件。

#### 格式化
```python
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```

#### list （可变）
list是一种有序的集合，可以随时添加和删除其中的元素。
```python
>>> classmates = ['Michael', 'Bob', 'Tracy']
>>> len(classmates)
3
>>> classmates[0]
'Michael'
>>> classmates[-1]
'Tracy'
>>> classmates.append('Adam')
>>> classmates
['Michael', 'Bob', 'Tracy', 'Adam']
>>> classmates.insert(1, 'Jack')
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy', 'Adam']
>>> classmates.pop()
'Adam'
>>> classmates.pop(1)
'Jack'
```

list里面的元素的数据类型也可以不同
```python
>>> L = ['Apple', 123, True]
>>> s = ['python', 'java', ['asp', 'php'], 'scheme']
>>> len(s)
4
```

#### tuple 元组 （不可变）
tuple和list非常类似，但是tuple一旦初始化就不能修改
```python
>>> classmates = ('Michael', 'Bob', 'Tracy')
```

#### if
```python
age = 3
if age >= 18:
    print('adult')
elif age >= 6:
    print('teenager')
else:
    print('kid')
```

#### for...in循环
```python
names = ['Michael', 'Bob', 'Tracy']
for name in names:
    print(name)
```

#### while循环
```python
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```

#### dict 字典
使用键-值（key-value）存储，具有极快的查找速度。
```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
>>> d.get('Thomas', -1)
-1
>>> d.pop('Bob')
75
```

#### set
```python
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}

>>> s.add(4)
>>> s.remove(4)

>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```
set和dict的唯一区别仅在于没有存储对应的value，但是，set的原理和dict一样，所以，同样不可以放入可变对象，因为无法判断两个可变对象是否相等，也就无法保证set内部“不会有重复元素”。

### 函数
空函数
```python
# pass作为占位符
def nop():
    pass
```

参数检查
```python
def my_abs(x):
    if not isinstance(x, (int, float)):
        raise TypeError('bad operand type')
    if x >= 0:
        return x
    else:
        return -x
```

返回多个值
```python
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny

>>> x, y = move(100, 100, 60, math.pi / 6)
```

但其实这只是一种假象，Python函数返回的仍然是单一值：
```python
>>> r = move(100, 100, 60, math.pi / 6)
>>> print(r)
(151.96152422706632, 70.0)
```
返回值是一个tuple，在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值，所以，Python的函数返回多值其实就是返回一个tuple。

#### 默认参数
```python
def enroll(name, gender, age=6, city='Beijing'):
```

#### 可变参数
```python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

如果已经有一个list或者tuple，要调用一个可变参数。
```python
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```

#### 关键字参数
关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。
```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)

>>> person('Michael', 30)
name: Michael age: 30 other: {}

>>> person('Adam', 45, gender='M', job='Engineer')
name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}

>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
```

#### 命名关键字参数
如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。
```python
def person(name, age, *, city, job):
    print(name, age, city, job)

>>> person('Jack', 24, city='Beijing', job='Engineer')
Jack 24 Beijing Engineer
```

如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了.
```python
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```

命名关键字参数可以有缺省值。
```python
def person(name, age, *, city='Beijing', job):
    print(name, age, city, job)
```

#### 参数组合
参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。
```python
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
```

#### 递归函数
使用递归函数需要注意防止栈溢出。在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出。

解决递归调用栈溢出的方法是通过**尾递归**优化，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的。
**尾递归**是指，在函数返回的时候，调用自身本身，并且，return语句不能包含表达式。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况。
```python
def fact(n):
    return fact_iter(n, 1)

def fact_iter(num, product):
    if num == 1:
        return product
    return fact_iter(num - 1, num * product)
```

#### Example
汉诺塔的移动。请编写move(n, a, b, c)函数，它接收参数n，表示3个柱子A、B、C中第1个柱子A的盘子数量，然后打印出把所有盘子从A借助B移动到C的方法。
```python
def move(n, a, b, c):
    if n == 1:
        print(a, '-->', c)
    else:
        move(n - 1, a, c, b)
        print(a, '-->', c)
        move(n - 1, b, a, c)
```

### 高级特性
#### 切片
```python
>>> L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']

>>> L[0:3]
['Michael', 'Sarah', 'Tracy']

>>> L[:3]
['Michael', 'Sarah', 'Tracy']

>>> L[-2:]
['Bob', 'Jack']
>>> L[-2:-1]
['Bob']
```
```python
>>> L = list(range(100))
>>> L
[0, 1, 2, 3, ..., 99]

# 前10个数，每两个取一个
>>> L[:10:2]
[0, 2, 4, 6, 8]

# 所有数，每5个取一个
>>> L[::5]
[0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95]

# 原样复制一个list
[0, 1, 2, 3, ..., 99]
>>> L[:]
[0, 1, 2, 3, ..., 99]
```
tuple也是一种list，唯一区别是tuple不可变。因此，tuple也可以用切片操作，只是操作的结果仍是tuple。

字符串也可以用切片操作，只是操作结果仍是字符串。
```python
>>> 'ABCDEFG'[:3]
'ABC'
>>> 'ABCDEFG'[::2]
'ACEG'
```

#### Example
利用切片操作，实现一个trim()函数，去除字符串首尾的空格。
```python
def trim(s):
    while(s[:1] == ' '):
        s = s[1:]
    while(s[-1:] == ' '):
        s = s[:-1]
    return s
```

#### 迭代
dict的迭代
```python
d = {'a': 1, 'b': 2, 'c': 3}
for key in d:
    print(key)
```
如果要迭代value，可以用`for value in d.values()`，如果要同时迭代key和value，可以用`for k, v in d.items()`。

字符串的迭代
```python
for ch in 'ABC':
    print(ch)
```

如何判断一个对象是可迭代对象呢？方法是通过collections模块的Iterable类型判断。
```python
>>> from collections import Iterable
>>> isinstance('abc', Iterable)     # str是否可迭代
True
```

Python内置的`enumerate`函数可以把一个list变成索引-元素对，这样就可以在`for`循环中同时迭代索引和元素本身。
```python
>>> for i, value in enumerate(['A', 'B', 'C']):
...     print(i, value)
...
0 A
1 B
2 C
```

```python
>>> for x, y in [(1, 1), (2, 4), (3, 9)]:
...     print(x, y)
...
1 1
2 4
3 9
```

#### 列表生成式
```python
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

for循环后面还可以加上if判断
```python
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```

还可以使用两层循环，可以生成全排列
```python
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

列表生成式也可以使用两个变量来生成list
```python
>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> [k + '=' + v for k, v in d.items()]
['y=B', 'x=A', 'z=C']
```

if ... else
```python
>>> [x if x % 2 == 0 else -x for x in range(1, 11)]
[-1, 2, -3, 4, -5, 6, -7, 8, -9, 10]
```

#### 生成器 generator
一边循环一边计算的机制，在循环的过程中不断推算出后续的元素，这样就不必创建完整的list，从而节省大量的空间。
```python
>>> g = (x * x for x in range(10))

>>> next(g)
0
>>> next(g)
1
```

迭代
```python
>>> g = (x * x for x in range(10))
>>> for n in g:
...     print(n)
```

斐波拉契数列
```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        print(b)
        a, b = b, a + b
        n = n + 1
    return 'done'

###########################
其中 a, b = b, a + b 等价于
t = (b, a + b)      # t是一个tuple
a = t[0]
b = t[1]
```

斐波拉契数列（使用generator）
```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'

>>> for n in fib(6):
...     print(n)
```
如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator。
变成generator的函数，在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。
generator函数的“调用”实际返回一个generator对象。

#### Example
杨辉三角
```python
def triangles():
    L = [1]
    while len(L) > 0:
        yield L
        L = [1] + [L[i] + L[i+1] for i in range(len(L)-1)] + [1]

n = 0
results = []
for t in triangles():
    results.append(t)
    n = n + 1
    if n == 10:
        break

for t in results:
    print(t)
```
输出结果
```python
[1]
[1, 1]
[1, 2, 1]
[1, 3, 3, 1]
[1, 4, 6, 4, 1]
[1, 5, 10, 10, 5, 1]
[1, 6, 15, 20, 15, 6, 1]
[1, 7, 21, 35, 35, 21, 7, 1]
[1, 8, 28, 56, 70, 56, 28, 8, 1]
[1, 9, 36, 84, 126, 126, 84, 36, 9, 1]
```

#### 迭代器
凡是可作用于`for`循环的对象都是`Iterable`类型；

凡是可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个惰性计算的序列，只有在需要返回下一个数据时它才会计算；

集合数据类型如`list`、`dict`、`str`等是`Iterable`但不是Iterator，不过可以通过iter()函数获得一个`Iterator`对象。

Python的`for`循环本质上就是通过不断调用`next()`函数实现的。

### 函数式编程 Functional Progamming

#### 高阶函数

变量可以指向函数
```python
>>> f = abs
>>> f(-10)
10
```

既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。
```python
def add(x, y, f):
    return f(x) + f(y)

print(add(-5, 6, abs))
```

##### map / reduce
`map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回。
```python
>>> def f(x):
...     return x * x
...
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r)
[1, 4, 9, 16, 25, 36, 49, 64, 81]

# 由于结果r是一个Iterator，Iterator是惰性序列，因此通过list()函数让它把整个序列都计算出来并返回一个list。
```

`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算。
```python
# 字符串转化为整数的函数
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def str2int(s):
    def fn(x, y):
        return x * 10 + y
    def char2num(s):
        return DIGITS[s]
    return reduce(fn, map(char2num, s))
```

##### filter
`filter()`函数用于过滤序列，也接收一个函数和一个序列。`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素。

在一个list中，删掉偶数，只保留奇数
```python
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
```

##### 用filter求素数
计算素数的一个方法是埃氏筛法：
1. 先把1删除（现今数学界1既不是素数也不是合数）
2. 读取队列中当前最小的数2，然后把2的倍数删去
3. 读取队列中当前最小的数3，然后把3的倍数删去
4. 读取队列中当前最小的数5，然后把5的倍数删去
5. 读取队列中当前最小的数7，然后把7的倍数删去
6. 如上所述直到需求的范围内所有的数均删除或读取
```python
# 先构造一个从3开始的奇数序列
def _odd_iter():
    n = 1
    while True:
        n = n + 2
        yield n
    
# 筛选函数
def _not_divisible(n):
    return lambda x: x % n > 0

# 生成器，不断返回下一个素数
def primes():
    yield 2
    it = _odd_iter()    # 初始序列
    while True:
        n = next(it)    # 返回序列的第一个数
        yield n
        it = filter(_not_divisible(n), it)   # 构造新序列

# 打印1000以内的素数:
for n in primes():
    if n < 1000:
        print(n)
    else:
        break
```

##### Example
回数是指从左向右读和从右向左读都是一样的数，例如12321，909。利用`filter()`筛选出回数。
```python
def is_palindrome(n):
    return str(n) == str(n)[::-1]

output = filter(is_palindrome, range(1000, 10000))
print('1000~10000:', list(output))
```

##### Sorted 排序
Python内置的`sorted()`函数就可以对`list`进行排序
```python
>>> sorted([36, 5, -12, 9, -21])
[-21, -12, 5, 9, 36]
```

`sorted()`函数也是一个高阶函数，它还可以接收一个`key`函数来实现自定义的排序，例如按绝对值大小排序：
```python
>>> sorted([36, 5, -12, 9, -21], key=abs)
[5, 9, -12, -21, 36]
```

字符串排序，默认是按照ASCII的大小比较的。
实现忽略大小写，按照字母序排序：
```python
>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)
['about', 'bob', 'Credit', 'Zoo']
```

反向排序，可以传入第三个参数`reverse=True`：
```python
>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
['Zoo', 'Credit', 'bob', 'about']
```

#### 返回函数
高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回。
```python
# 求和
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum

>>> f = lazy_sum(1, 3, 5, 7, 9)
>>> f()
25
```

#### 匿名函数
```python
>>> list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
[1, 4, 9, 16, 25, 36, 49, 64, 81]

# 匿名函数lambda x: x * x 实际上是：
def f(x):
    return x * x
```
关键字lambda表示匿名函数，冒号前面的x表示函数参数。

匿名函数只能有一个表达式，不用写return，返回值就是该表达式的结果。

### 模块

#### 使用模块
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '

__author__ = 'yuan'

import sys

def test():
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')

if __name__=='__main__':
    test()
```
第1行和第2行是标准注释，第1行注释可以让这个hello.py文件直接在Unix/Linux/Mac上运行，第2行注释表示.py文件本身使用标准UTF-8编码；

第4行是一个字符串，表示模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释；

第6行使用__author__变量把作者写进去。

`sys`模块有一个`argv`变量，用`list`存储了命令行的所有参数。`argv`至少有一个元素，因为第一个参数永远是该.py文件的名称，例如：

运行`python3 hello.py`获得的`sys.argv`就是`['hello.py']`；

运行`python3 hello.py Michael`获得的`sys.argv`就是`['hello.py', 'Michael']`。

当我们在命令行运行`hello`模块文件时，Python解释器把一个特殊变量`__name__`置为`__main__`，而如果在其他地方导入该`hello`模块时，`if`判断将失败，因此，这种`if`测试可以让一个模块通过命令行运行时执行一些额外的代码，最常见的就是运行测试。

#### 作用域
正常的函数和变量名是公开的（public），可以被直接引用，如`abc`。

类似`__xxx__`这样的变量是特殊变量，可以被直接引用，但是有特殊用途，如`__name__`，我们自己的变量一般不要用这种变量名。

类似`_xxx`和`__xxx`这样的函数或变量就是非公开的（private），不应该被直接引用，如`_abc`，`__abc`。

private函数和变量“不应该”被直接引用，而不是“不能”被直接引用，因为Python并没有一种方法可以完全限制访问private函数或变量。

#### 安装第三方模块
安装第三方模块，是通过包管理工具pip完成的。
```python
pip install Pillow
```

### 面向对象编程
```python
class Student(object):

    def __init__(self, name, score):
        self.__name = name
        self.__score = score

    def get_name(self):
        return self.__name

    def get_score(self):
        return self.__score
```

#### 获取对象信息

##### 使用`type()`

```python
>>> type(123)
<class 'int'>
>>> type('str')
<class 'str'>
>>> type(None)
<type(None) 'NoneType'>
>>> type(abs)
<class 'builtin_function_or_method'>
```

##### 使用`isinstance()`

```python
>>> isinstance('a', str)
True
>>> isinstance(123, int)
True
```

还可以判断一个变量是否是某些类型中的一种

```python
>>> isinstance([1, 2, 3], (list, tuple))
True
>>> isinstance((1, 2, 3), (list, tuple))
True
```

##### 使用`dir()`

可以获得一个对象的所有属性和方法，它返回一个包含字符串的list

```python
>>> dir('ABC')
['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
```

### 面向对象高级编程

#### 使用@property

```python
class Student(object):

    # birth 可读写属性
    @property
    def birth(self):
        return self._birth

    @birth.setter
    def birth(self, value):
        self._birth = value

    # age 只读属性
    @property
    def age(self):
        return 2015 - self._birth
    
>>> s = Student()
>>> s.birth = 1994
>>> s.birth
1994
```

#### 定制类

##### \_\_str\_\_

```python
class Student(object):
    def __init__(self, name):
        self.name = name
        
    # >>> print(s)
    def __str__(self):
        return 'Student object (name=%s)' % self.name
    
    # >>> s
    __repr__ = __str__
 
>>> s = Student('Michael')
>>> print(s)
Student object (name: Michael)
>>> s
Student object (name: Michael)
```

##### \_\_iter\_\_

如果一个类想被用于`for ... in`循环，类似list或tuple那样，就必须实现一个`__iter__()`方法，该方法返回一个迭代对象，然后，Python的for循环就会不断调用该迭代对象的`__next__()`方法拿到循环的下一个值，直到遇到`StopIteration`错误时退出循环。

```python
class Fib(object):
    def __init__(self):
        self.a, self.b = 0, 1 # 初始化两个计数器a，b

    def __iter__(self):
        return self # 实例本身就是迭代对象，故返回自己

    def __next__(self):
        self.a, self.b = self.b, self.a + self.b # 计算下一个值，先计算等号右边，再赋值给左边
        if self.a > 100000: # 退出循环的条件
            raise StopIteration()
        return self.a # 返回下一个值
```

##### \_\_getattr\_\_

```python
class Chain(object):

    def __init__(self, path=''):
        self._path = path

    def __getattr__(self, path):
        return Chain('%s/%s' % (self._path, path))

    def __str__(self):
        return self._path

    __repr__ = __str__
    
>>> Chain().status.user.timeline.list
'/status/user/timeline/list'
```

#### 枚举

```python
from enum import Enum

Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
```

自定义枚举类

```python
from enum import Enum, unique

@unique
class Weekday(Enum):
    Sun = 0     # Sun的value被设定为0（枚举默认从1开始计数）
    Mon = 1
    Tue = 2
    Wed = 3
    Thu = 4
    Fri = 5
    Sat = 6
```

### 错误处理

#### try...except

```python
try:
    print('try...')
    r = 10 / 0
    print('result:', r)
except ZeroDivisionError as e:
    print('except:', e)
finally:
    print('finally...')
print('END')

# 输出
try...
except: division by zero
finally...
END
```

#### 记录错误

```python
import logging

try:
    pass
except Exception as e:
    logging.exception(e)
```

#### 抛出错误

用`raise`语句抛出错误

```python
def foo(s):
    n = int(s)
    if n==0:
        raise ValueError('invalid value: %s' % s)    # 抛出错误
    return 10 / n

def bar():
    try:
        foo('0')
    except ValueError as e:
        print('ValueError!')
        raise        # raise不带参数，会把当前错误原样抛出。
```

### IO编程

#### 文件读写

##### 读文件

```python
try:
    f = open('/path/to/file', 'r')
    print(f.read())
finally:
    if f:
        f.close()
        
# 等同于
with open('/path/to/file', 'r') as f:
    print(f.read())
```

- `f.read()`    一次性读取文件的全部内容
- `f.read(size)`    每次最多读取size个字节的内容
- `f.readline()`    每次读取一行内容
- `f.readlines()`    一次读取所有内容并按行返回list

##### file-like Object

像`open()`函数返回的这种有个`read()`方法的对象，在Python中统称为file-like Object。除了file外，还可以是内存的字节流，网络流，自定义流等等。file-like Object不要求从特定类继承，只要写个`read()`方法就行。

`StringIO`就是在内存中创建的file-like Object，常用作临时缓冲。

##### 二进制文件

前面讲的默认都是读取文本文件，并且是UTF-8编码的文本文件。要读取二进制文件，比如图片、视频等等，用`'rb'`模式打开文件。

```python
>>> f = open('/Users/michael/test.jpg', 'rb')
>>> f.read()
b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节
```

##### 字符编码

要读取非UTF-8编码的文本文件，需要给`open()`函数传入`encoding`参数，例如，读取GBK编码的文件：

```python
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk')
```

`open()`函数还接收一个`errors`参数，表示如果遇到编码错误后如何处理。最简单的方式是直接忽略：

```python
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk', errors='ignore')
```

##### 写文件

调用`open()`函数时，传入标识符`'w'`或者`'wb'`表示写文本文件或写二进制文件：

```python
>>> f = open('/Users/michael/test.txt', 'w')
>>> f.write('Hello, world!')
>>> f.close()
```

当我们写文件时，操作系统往往不会立刻把数据写入磁盘，而是放到内存缓存起来，空闲的时候再慢慢写入。只有调用`close()`方法时，操作系统才保证把没有写入的数据全部写入磁盘。忘记调用`close()`的后果是数据可能只写了一部分到磁盘，剩下的丢失了。

```python
# 使用with语句
with open('/Users/michael/test.txt', 'w') as f:
    f.write('Hello, world!')
```

要写入特定编码的文本文件，要给`open()`函数传入`encoding`参数，将字符串自动转换成指定编码。

以`'w'`模式写入文件时，如果文件已存在，会直接覆盖（相当于删掉后新写入一个文件）。可以传入`'a'`以追加（append）模式写入。

#### StringIO和BytesIO

##### StringIO

StringIO就是在内存中读写str

```python
>>> from io import StringIO
>>> f = StringIO()
>>> f.write('hello')
5
>>> f.write(' ')
1
>>> f.write('world!')
6
>>> print(f.getvalue())
hello world!
```

读取StringIO

```python
>>> from io import StringIO
>>> f = StringIO('Hello!\nHi!\nGoodbye!')
>>> while True:
...     s = f.readline()
...     if s == '':
...         break
...     print(s.strip())
...
Hello!
Hi!
Goodbye!
```

##### BytesIO

StringIO操作的只能是str，如果要操作二进制数据，就需要使用BytesIO。

```python
>>> from io import BytesIO
>>> f = BytesIO()
>>> f.write('中文'.encode('utf-8'))
6
>>> print(f.getvalue())
b'\xe4\xb8\xad\xe6\x96\x87'
```

#### 操作文件和目录

```python
>>> import os
# 查看当前目录的绝对路径:
>>> os.path.abspath('.')
'/Users/michael'
# 在某个目录下创建一个新目录，首先把新目录的完整路径表示出来:
>>> os.path.join('/Users/michael', 'testdir')
'/Users/michael/testdir'
# 然后创建一个目录:
>>> os.mkdir('/Users/michael/testdir')
# 删掉一个目录:
>>> os.rmdir('/Users/michael/testdir')
```

把两个路径合成一个时，不要直接拼字符串，而要通过`os.path.join()`函数，这样可以正确处理不同操作系统的路径分隔符。

列出当前目录下的所有目录

```python
>>> [x for x in os.listdir('.') if os.path.isdir(x)]
```

列出所有的`.py`文件

```python
>>> [x for x in os.listdir('.') if os.path.isfile(x) and os.path.splitext(x)[1]=='.py']
```

#### 序列化

把变量从内存中变成可存储或传输的过程称之为序列化，在Python中叫pickling，在其他语言中也被称之为serialization，marshalling，flattening等等。

序列化之后，就可以把序列化后的内容写入磁盘，或者通过网络传输到别的机器上。

反过来，把变量内容从序列化的对象重新读到内存里称之为反序列化，即unpickling。

Python提供了`pickle`模块来实现序列化。

```python
>>> import pickle
>>> d = dict(name='Bob', age=20, score=88)
>>> pickle.dumps(d)
b'\x80\x03}q\x00(X\x03\x00\x00\x00ageq\x01K\x14X\x05\x00\x00\x00scoreq\x02KXX\x04\x00\x00\x00nameq\x03X\x03\x00\x00\x00Bobq\x04u.'
```

`pickle.dumps()`方法把任意对象序列化成一个`bytes`，然后，就可以把这个`bytes`写入文件。

或者用`pickle.dump()`直接把对象序列化后写入一个file-like Object：

```python
>>> f = open('dump.txt', 'wb')
>>> pickle.dump(d, f)
>>> f.close()
```

反序列化

```python
>>> f = open('dump.txt', 'rb')
>>> d = pickle.load(f)
>>> f.close()
>>> d
{'age': 20, 'score': 88, 'name': 'Bob'}
```

也可以先把内容读到一个`bytes`，然后用`pickle.loads()`方法反序列化出对象。

##### JSON

把Python对象变成一个JSON

```python
>>> import json
>>> d = dict(name='Bob', age=20, score=88)
>>> json.dumps(d)
'{"age": 20, "score": 88, "name": "Bob"}'
```

把JSON反序列化为Python对象

```python
>>> json_str = '{"age": 20, "score": 88, "name": "Bob"}'
>>> json.loads(json_str)
{'age': 20, 'score': 88, 'name': 'Bob'}
```

### 正则表达式

`\d`可以匹配一个数字，`\w`可以匹配一个字母或数字，`\s`可以匹配一个空格，`.`可以匹配任意字符，`*`表示任意个字符（包括0个），用`+`表示至少一个字符，用`?`表示0个或1个字符，用`{n}`表示n个字符，用`{n,m}`表示n-m个字符。

`A|B`可以匹配A或B，所以`(P|p)ython`可以匹配`'Python'`或者`'python'`。

`^`表示行的开头，`^\d`表示必须以数字开头。

`$`表示行的结束，`\d$`表示必须以数字结束。