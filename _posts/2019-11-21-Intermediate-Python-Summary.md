---
layout: post
title:  "《Intermediate Python》读后总结"
date:   2019-11-21 07:30:00 +0800
categories: python 
tags: python
excerpt: 《Intermediate Python》，即python进阶，包含很多妙用
---
* content
{:toc}

# 1、Programmer tools
## 1.1 Virtual Environment
### virtualenv是什么？
*virtualenv*是年迈pythoner的必备良品，主要用来做环境隔离。
比如说你维护的代码一个要求python2，一个要求python3，
一个要求django1.0，一个要求django2.0，要是使用系统的环境，就会不断的切版本，升降级，尤其是部署到集成环境时，可能会产生更为严重的冲突。
### 如何解决？
使用*virtualenv*！针对每个程序创建独立（隔离）的Python环境，而不是在全局安装所依赖的模块。
```
$ pip install virtualenv
$ virtualenv myproject

$ source myproject/bin/activate
$ deactivate
```
执行第一个命令在myproject文件夹创建一个隔离的virtualenv环境，第二个命令激活这个隔离的环境(virtualenv)。

如果你想让你的virtualenv使用系统全局模块，请使用--system-site-packages参数创建你的virtualenv，例如：
```
virtualenv --system-site-packages mycoolproject
```


## 1.2 Debugging
pdb是python调试用的库，但是我觉得还是IDE的debug模式方便些。
### 命令行
```
$ python -m pdb my_script.py
```
这会触发debugger在脚本第一行指令处停止执行。这在脚本很短时会很有帮助。你可以通过(Pdb)模式接着查看变量信息，并且逐行调试。


### 脚本内部运行
```
import pdb
def make_bread():
    pdb.set_trace()
    return "I don't have time"
print(make_bread())
```
命令列表：
* c: 继续执行
* w: 显示当前正在执行的代码行的上下文信息
* a: 打印当前函数的参数列表
* s: 执行当前代码行，并停在第一个能停的地方（相当于单步进入）
* n: 继续执行到当前函数的下一行，或者当前行直接返回（单步跳过）

单步跳过（next）和单步进入（step）的区别在于， 单步进入会进入当前行调用的函数内部并停在里面， 而单步跳过会（几乎）全速执行完当前行调用的函数，并停在当前函数的下一行。

## 1.3 对象自省（Object introspection）
自省(introspection)，在计算机编程领域里，是指在运行时来判断一个对象的类型的能力。它是Python的强项之一。Python中所有一切都是一个对象，而且我们可以仔细勘察那些对象。
### dir
它返回一个列表，列出了一个对象所拥有的属性和方法。这里是一个例子：
```
my_list = [1, 2, 3]
dir(my_list)
# Output: ['__add__', '__class__', '__contains__', '__delattr__', '__delitem__',
# '__delslice__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__',
# '__getitem__', '__getslice__', '__gt__', '__hash__', '__iadd__', '__imul__',
# '__init__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__',
# '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__',
# '__setattr__', '__setitem__', '__setslice__', '__sizeof__', '__str__',
# '__subclasshook__', 'append', 'count', 'extend', 'index', 'insert', 'pop',
# 'remove', 'reverse', 'sort']
```
上面的自省给了我们一个列表对象的所有方法的名字。

如果我们运行dir()而不传入参数，那么它会返回当前作用域的所有名字。

### type 和 id
*type*函数返回一个对象的类型。
```
print(type(''))
# Output: <type 'str'>
print(type([]))
# Output: <type 'list'>
print(type({}))
# Output: <type 'dict'>
print(type(dict))
# Output: <type 'type'>
print(type(3))
# Output: <type 'int'>
```
*id()*函数返回任意不同种类对象的唯一ID
```
name = "Yasoob"
print(id(name))
# Output: 139972439030304
```
### inspect
*inspect*模块也提供了许多有用的函数，来获取活跃对象的信息。比方说，你可以查看一个对象的成员，只需运行
```
import inspect
print(inspect.getmembers(str))
# Output: [('__add__', <slot wrapper '__add__' of ... ...
```
# 2、语法
## 2.1 Exceptions
最基本的术语里我们知道了try/except从句。可能触发异常产生的代码会放到try语句块里，而处理异常的代码会在except语句块里实现。这是一个简单的例子：
```
try:
    file = open('test.txt', 'rb')
except IOError as e:
    print('An IOError occurred. {}'.format(e.args[-1]))
```
### 处理多个异常
第一种方法需要把所有可能发生的异常放到一个元组里。
```
try:
    file = open('test.txt', 'rb')
except (IOError, EOFError) as e:
    print("An error occurred. {}".format(e.args[-1]))
```
另外一种方式是对每个单独的异常在单独的except语句块中处理。我们想要多少个except语句块都可以。
如果异常没有被第一个except语句块处理，那么它也许被下一个语句块处理，或者根本不会被处理
```
try:
    file = open('test.txt', 'rb')
except EOFError as e:
    print("An EOF error occurred.")
    raise e
except IOError as e:
    print("An error occurred.")
    raise e
```
最后一种方式会捕获所有异常
```
try:
    file = open('test.txt', 'rb')
except Exception:
    # 打印一些异常日志，如果你想要的话
    raise
```

### finally 从句
包裹到finally从句中的代码不管异常是否触发都将会被执行。这可以被用来在脚本执行之后做清理工作。
```
try:
    file = open('test.txt', 'rb')
except IOError as e:
    print('An IOError occurred. {}'.format(e.args[-1]))
finally:
    print("This would be printed whether or not an exception occurred!")
# Output: An IOError occurred. No such file or directory
# This would be printed whether or not an exception occurred!
```

### try/else 从句
else从句只会在没有异常的情况下执行，而且它会在finally语句之前执行。
```
try:
    print('I am sure no exception is going to occur!')
except Exception:
    print('exception')
else:
    # 这里的代码只会在try语句里没有触发异常时运行,
    # 但是这里的异常将 *不会* 被捕获
    print('This would only run if no exception occurs. And an error here '
          'would NOT be caught.')
finally:
    print('This would be printed in every case.')
# Output: I am sure no exception is going to occur!
# This would only run if no exception occurs.
# This would be printed in every case.
```
## 2.2 For - Else
for循环有一个else从句，这个else从句会在循环正常结束时执行。这意味着，循环没有遇到任何break.

有个常见的构造是跑一个循环，并查找一个元素。如果这个元素被找到了，我们使用break来中断这个循环。有两个场景会让循环停下来。

* 第一个是当一个元素被找到，break被触发。
* 第二个场景是循环结束。
现在我们也许想知道其中哪一个，才是导致循环完成的原因。一个方法是先设置一个标记，然后在循环结束时打上标记。另一个是使用else从句。

比如说找质数，如果是正常for,需要在里面增加flag判断
```
for n in range(2, 10):
    for x in range(2, n):
        if n % x == 0:
            print(n, 'equals', x, '*', n / x)
            break

```
加入else，会简单很多
```
for n in range(2, 10):
    for x in range(2, n):
        if n % x == 0:
            print(n, 'equals', x, '*', n / x)
            break
    else:
        # loop fell through without finding a factor
        print(n, 'is a prime number')
```

## 2.3 三元表达式

```
#如果条件为真，返回真 否则返回假
condition_is_true if condition else condition_is_false
```
还有一种比较晦涩的使用了元组的三元表达式，不常用。
```
#(返回假，返回真)[真或假]
(if_test_is_false, if_test_is_true)[test]
```
举个栗子：
```
fat = True
fitness = ("skinny", "fat")[fat]
print("Ali is", fitness)
#输出: Ali is fat
```
元组三元表达式会执行两个元组值后，再去判断真假；而if else 则if 条件为false时才会执行else语句。

## 2.4 Global & Return
global代替return:声明global变量。
```
def add(value1,value2):
    global result
    result = value1 + value2
add(3,5)
print(result)
# Output: 8
```

一般情况尽量避免使用global，会引入全局域，使语义变复杂。
### 一次返回多个值
返回一个包含多个值的tuple(元组)，list(列表)或者dict(字典)
```
def profile():
    name = "Danny"
    age = 30
    return (name, age)
profile_data = profile()
print(profile_data[0])
# Output: Danny
print(profile_data[1])
# Output: 30

# 更常见的方式：也是返回元组，只不过省略了括号
def profile():
    name = "Danny"
    age = 30
    return name, age
```
## 2.5 open函数
```
with open('photo.jpg', 'r+') as f:
    jpgdata = f.read()
```
open的第一个参数是文件名。第二个(mode 打开模式)决定了这个文件如何被打开。

* 如果你想读取文件，传入r
* 如果你想读取并写入文件，传入r+
* 如果你想覆盖写入文件，传入w
* 如果你想在文件末尾附加内容，传入a
* 二进制加 b

注意项：
1. 是否有异常时的close处理：使用with open('photo.jpg', 'rb') as inf:
2. 注意编码，python2不支持编码，但是可以用io替代 
`with io.open('summary.txt', 'w', encoding='utf-8') as outf:`

 
## 2.6 *args and **kwargs
*args 是用来发送一个非键值对的可变数量的参数列表给一个函数.
**kwargs 允许你将不定长度的键值对, 作为参数传递给一个函数。 
### 组合
```
# 首先使用 *args
>>> args = ("two", 3, 5)
>>> test_args_kwargs(*args)
arg1: two
arg2: 3
arg3: 5
# 现在使用 **kwargs:
>>> kwargs = {"arg3": 3, "arg2": "two", "arg1": 5}
>>> test_args_kwargs(**kwargs)
arg1: 5
arg2: two
arg3: 3
```

### 顺序
`some_func(fargs, *args, **kwargs)`
## 2.7 上下文管理器
上下文管理器允许你在有需要的时候，精确地分配和释放资源。
使用上下文管理器最广泛的案例就是with语句了。
```
with open('some_file', 'w') as opened_file:
    opened_file.write('Hola!')
```
等价于
```
file = open('some_file', 'w')
try:
    file.write('Hola!')
finally:
    file.close()
```

### 基于类的实现
一个上下文管理器的类，最起码要定义__enter__和__exit__方法。
```
class File(object):
    def __init__(self, file_name, method):
        self.file_obj = open(file_name, method)
    def __enter__(self):
        return self.file_obj
    def __exit__(self, type, value, traceback):
        self.file_obj.close()
```
通过定义__enter__和__exit__方法，我们可以在with语句里使用它。我们来试试：
```
with File('demo.txt', 'w') as opened_file:
    opened_file.write('Hola!')
```
__exit__函数接受三个参数:type, value和traceback。
当异常发生时，with语句会采取哪些步骤：
1. 它把异常的type,value和traceback传递给__exit__方法
2. 它让__exit__方法来处理异常
3. 如果__exit__返回的是True，那么这个异常就被优雅地处理了。
4. 如果__exit__返回的是True以外的任何东西，那么这个异常将被with语句抛出。
```
class File(object):
    def __init__(self, file_name, method):
        self.file_obj = open(file_name, method)
    def __enter__(self):
        return self.file_obj
    def __exit__(self, type, value, traceback):
        print("Exception has been handled")
        self.file_obj.close()
        return True
with File('demo.txt', 'w') as opened_file:
    opened_file.undefined_function()
# Output: Exception has been handled
```
__exit__方法返回了True,因此没有异常会被with语句抛出。

### 基于生成器的实现
还可以用装饰器和生成器来实现上下文管理器
```
from contextlib import contextmanager
@contextmanager
def open_file(name):
    f = open(name, 'w')
    yield f
    f.close()
```
处理逻辑
```
1. Python解释器遇到了yield关键字。因为这个缘故它创建了一个生成器而不是一个普通的函数。
2. 因为这个装饰器，contextmanager会被调用并传入函数名（open_file）作为参数。
3. contextmanager函数返回一个以GeneratorContextManager对象封装过的生成器。
4. 这个GeneratorContextManager被赋值给open_file函数，我们实际上是在调用GeneratorContextManager对象。
```
使用
```
with open_file('some_file') as f:
    f.write('hola!')
```

# 3、函数式编程
## 3.1 Enumerate
枚举(enumerate)是Python内置函数，可以在遍历数据时自动计数
```
for counter, value in enumerate(some_list):
    print(counter, value)
```
可以指定开始值
```
my_list = ['apple', 'banana', 'grapes', 'pear']
for c, value in enumerate(my_list, 1):
    print(c, value)
# 输出:
(1, 'apple')
(2, 'banana')
(3, 'grapes')
(4, 'pear')
```
## 3.2 Lambdas
lambda表达式是一行函数，也叫匿名函数。
`lambda 参数:操作(参数)`
```
add = lambda x, y: x + y
print(add(3, 5))
# Output: 8

# 列表排序
a = [(1, 2), (4, 1), (9, 10), (13, -3)]
a.sort(key=lambda x: x[1])
print(a)
# Output: [(13, -3), (4, 1), (1, 2), (9, 10)]
```
## 3.3 set数据结构
* 创建set
```
a_set = {'red', 'blue', 'green'}
print(type(a_set))
### 输出: <type 'set'>
```
* 去除重复项
```
some_list = ['a', 'b', 'c', 'b', 'd', 'm', 'n', 'n']
duplicates = set([x for x in some_list if some_list.count(x) > 1])
print(duplicates)
### 输出: set(['b', 'n'])
```
* 交集
```
valid = set(['yellow', 'red', 'blue', 'green', 'black'])
input_set = set(['red', 'brown'])
print(input_set.intersection(valid))
### 输出: set(['red'])
```
* 差集
```
valid = set(['yellow', 'red', 'blue', 'green', 'black'])
input_set = set(['red', 'brown'])
print(input_set.difference(valid))
### 输出: set(['brown'])
```

## 3.4 Map & Filter & reduce
### map
Map会将一个函数映射到一个输入列表的所有元素上。
`map(function_to_apply, list_of_inputs)`
```
items = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, items))
```
### filter
filter过滤列表中的元素，并且返回一个由所有符合要求的元素所构成的列表，符合要求即函数映射到该元素时返回值为True。
```
number_list = range(-5, 5)
less_than_zero = filter(lambda x: x < 0, number_list)
print(list(less_than_zero))  
# 译者注：上面print时，加了list转换，是为了python2/3的兼容性
#        在python2中filter直接返回列表，但在python3中返回迭代器
#        因此为了兼容python3, 需要list转换一下
# Output: [-5, -4, -3, -2, -1]
```

### reduce
当需要对一个列表进行一些计算并返回结果时，可以使用Reduce。

```
from functools import reduce
product = reduce( (lambda x, y: x * y), [1, 2, 3, 4] )
# Output: 24
```
python3已经无法直接使用，需要额外引入

## 3.5 推导式
### 列表推导式

`variable = [out_exp for out_exp in input_list if out_exp == 2]`
### 字典推导式
```
mcase = {'a': 10, 'b': 34, 'A': 7, 'Z': 3}
mcase_frequency = {
    k.lower(): mcase.get(k.lower(), 0) + mcase.get(k.upper(), 0)
    for k in mcase.keys()
}
# mcase_frequency == {'a': 17, 'z': 3, 'b': 34}
```
```
#dict键值互换
{v: k for k, v in some_dict.items()}
```
### 集合推导式
```
squared = {x**2 for x in [1, 1, 2]}
print(squared)
# Output: {1, 4}
```
# 4、数据结构
## 4.1 生成器
### 可迭代对象(Iterable)
Python中任意的对象，只要它定义了可以返回一个迭代器的__iter__方法，或者定义了可以支持下标索引的__getitem__方法，那么它就是一个可迭代对象。
### 迭代器(Iterator)
任意对象，只要定义了next(Python2) 或者__next__方法，它就是一个迭代器。
### 迭代(Iteration)
当我们使用一个循环来遍历某个东西时，这个过程本身就叫迭代。
### 生成器（Generators）
生成器也是一种迭代器，但是你只能对其迭代一次。这是因为它们并没有把所有的值存在内存中，而是在运行时生成值。
它们并不返回一个值，而是yield(暂且译作“生出”)一个值。
```
def generator_function():
    for i in range(10):
        yield i
for item in generator_function():
    print(item)
# Output: 0
# 1
# 2
...
# 9
```
生成器最佳应用场景是：你不想同一时间将所有计算出来的大量结果集分配到内存当中，特别是结果集里还包含循环。


## 4.2 协程（Coroutines）
Python中的协程和生成器很相似但又稍有不同。主要区别在于：

* 生成器是数据的生产者
* 协程则是数据的消费者

```
search = grep('coroutine')
next(search)
#output: Searching for coroutine
search.send("I love you")
search.send("Don't you love me?")
search.send("I love coroutine instead!")
#output: I love coroutine instead!

```
发送的值会被yield接收。我们为什么要运行next()方法呢？这样做正是为了启动一个协程。就像协程中包含的生成器并不是立刻执行，而是通过next()方法来响应send()方法。因此，你必须通过next()方法来执行yield表达式。

我们可以通过调用close()方法来关闭一个协程。像这样：
```
search = grep('coroutine')
search.close()
```
## 4.3 Classes
### 类变量和实例变量
```
class SuperClass(object):
    pi = 3.14
    superpowers = []

    def __init__(self, name):
        self.name = name

    def add_superpower(self, power):
        self.superpowers.append(power)

foo = SuperClass('foo')
bar = SuperClass('bar')
foo.name
# Output: 'foo'

bar.name
# Output: 'bar'


foo.add_superpower('fly')
bar.superpowers
# Output: ['fly']

foo.superpowers
# Output: ['fly']
foo.pi
# 3.14
bar.pi
# 3.14
foo.pi = 300
foo.pi
# 300
bar.pi
# 3.14s
```
### 旧式类和新式类
* 旧式类不继承任何内容
* 新式类继承自object
* python3全是新式类

### 魔法方法
* __init__
创建实例时执行

* __getitem__
实现了这个方法，可以用下标获取属性

```
class GetTest(object):
    def __init__(self):
        self.info = {
            'name':'Yasoob',
            'country':'Pakistan',
            'number':12345812
        }

    def __getitem__(self,i):
        return self.info[i]

foo = GetTest()

foo['name']
# Output: 'Yasoob'

foo['number']
# Output: 12345812
```

# 5、Data types
## 5.1 Collections
### defaultdict
* 与dict类型不同，你不需要检查key是否存在

```
from collections import defaultdict
colours = (
    ('Yasoob', 'Yellow'),
    ('Ali', 'Blue'),
    ('Arham', 'Green'),
    ('Ali', 'Black'),
    ('Yasoob', 'Red'),
    ('Ahmed', 'Silver'),
)
favourite_colours = defaultdict(list)
for name, colour in colours:
    favourite_colours[name].append(colour)
print(favourite_colours)
# defaultdict(<type 'list'>,
#    {'Arham': ['Green'],
#     'Yasoob': ['Yellow', 'Red'],
#     'Ahmed': ['Silver'],
#     'Ali': ['Blue', 'Black']
# })
```
* 当你在一个字典中对一个键进行嵌套赋值时，如果这个键不存在，会触发keyError异常。 defaultdict允许我们用一个聪明的方式绕过这个问题。

```
#问题
some_dict = {}
some_dict['colours']['favourite'] = "yellow"
##异常输出：KeyError: 'colours'
```
```
#解决方案
import collections
tree = lambda: collections.defaultdict(tree)
some_dict = tree()
some_dict['colours']['favourite'] = "yellow"
##运行正常
```
### counter
Counter是一个计数器，它可以帮助我们针对某项数据进行计数
```
from collections import Counter
colours = (
    ('Yasoob', 'Yellow'),
    ('Ali', 'Blue'),
    ('Arham', 'Green'),
    ('Ali', 'Black'),
    ('Yasoob', 'Red'),
    ('Ahmed', 'Silver'),
)
favs = Counter(name for name, colour in colours)
print(favs)
## 输出:
## Counter({
##     'Yasoob': 2,
##     'Ali': 2,
##     'Arham': 1,
##     'Ahmed': 1
##  })
```

### deque
deque提供了一个双端队列，你可以从头/尾两端添加或删除元素。
```
from collections import deque
d = deque()
d.append('1')
d.append('2')
d.append('3')
print(len(d))
## 输出: 3
print(d[0])
## 输出: '1'
print(d[-1])
## 输出: '3'


d = deque(range(5))
print(len(d))
## 输出: 5
d.popleft()
## 输出: 0
d.pop()
## 输出: 4
print(d)
## 输出: deque([1, 2, 3])
```
我们也可以限制这个列表的大小
```
d = deque(maxlen=30)
d = deque([1,2,3,4,5])
d.extendleft([0])
d.extend([6,7,8])
print(d)
## 输出: deque([0, 1, 2, 3, 4, 5, 6, 7, 8])
```

### namedtuple
```
from collections import namedtuple
Animal = namedtuple('Animal', 'name age type')
perry = Animal(name="perry", age=31, type="cat")
print(perry)
## 输出: Animal(name='perry', age=31, type='cat')
print(perry.name)
## 输出: 'perry'
print(perry[0])
## 输出: 'perry'
```


### enum.Enum (Python 3.4+)
```
from collections import namedtuple
from enum import Enum
class Species(Enum):
    cat = 1
    dog = 2
    horse = 3

Animal = namedtuple('Animal', 'name age type')
perry = Animal(name="Perry", age=31, type=Species.cat)
drogon = Animal(name="Drogon", age=4, type=Species.dragon)
tom = Animal(name="Tom", age=75, type=Species.cat)
```
```
#有三种方法访问枚举数据，例如以下方法都可以获取到’cat’的值：
Species(1)
Species['cat']
Species.cat
```


## 5.2 对象变动(Mutation)
```
foo = ['hi']
print(foo)
# Output: ['hi']
bar = foo
bar += ['bye']
print(foo)
# Output: ['hi', 'bye']
```
对象可变性(mutability)可能会引起一些问题：
```
def add_to(num, target=[]):
    target.append(num)
    return target
add_to(1)
# Output: [1]
add_to(2)
# Output: [1, 2]
add_to(3)
# Output: [1, 2, 3]
```
通过一些方法规避该类问题
```
def add_to(element, target=None):
    if target is None:
        target = []
    target.append(element)
    return target
```
## 5.3 __slots__ Magic
默认情况下Python用一个字典来保存一个对象的实例属性。
使用__slots__来告诉Python不要使用字典，而且只给一个固定集合的属性分配空间
* 不使用__slots__
```
class MyClass(object):
  def __init__(self, name, identifier):
      self.name = name
      self.identifier = identifier
      self.set_up()
  # ...
```
* 使用__slots__
```
class MyClass(object):
  __slots__ = ['name', 'identifier']
  def __init__(self, name, identifier):
      self.name = name
      self.identifier = identifier
      self.set_up()
  # ...
```
第二段代码会为你的内存减轻负担,内存占用率几乎40%~50%的减少。

# 6、Decorators
## 6.1 What is a decorator?
```
def a_new_decorator(a_func):
    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")
        a_func()
        print("I am doing some boring work after executing a_func()")
    return wrapTheFunction

@a_new_decorator
def a_function_requiring_decoration():
    """Hey you! Decorate me!"""
    print("I am the function which needs some decoration to "
          "remove my foul smell")
a_function_requiring_decoration()
#outputs: I am doing some boring work before executing a_func()
#         I am the function which needs some decoration to remove my foul smell
#         I am doing some boring work after executing a_func()
#the @a_new_decorator is just a short way of saying:
a_function_requiring_decoration = a_new_decorator(a_function_requiring_decoration)

```
但是还存在个小瑕疵,可以通过wraps解决
```
print(a_function_requiring_decoration.__name__)
# Output: wrapTheFunction
```
```
from functools import wraps
def a_new_decorator(a_func):
    @wraps(a_func)
    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")
        a_func()
        print("I am doing some boring work after executing a_func()")
    return wrapTheFunction
@a_new_decorator
def a_function_requiring_decoration():
    """Hey yo! Decorate me!"""
    print("I am the function which needs some decoration to "
          "remove my foul smell")
print(a_function_requiring_decoration.__name__)
# Output: a_function_requiring_decoration
```
* 授权

```
from functools import wraps
def requires_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        auth = request.authorization
        if not auth or not check_auth(auth.username, auth.password):
            authenticate()
        return f(*args, **kwargs)
    return decorated
```
* 日志

```
from functools import wraps
def logit(func):
    @wraps(func)
    def with_logging(*args, **kwargs):
        print(func.__name__ + " was called")
        return func(*args, **kwargs)
    return with_logging
@logit
def addition_func(x):
   """Do some math."""
   return x + x
result = addition_func(4)
# Output: addition_func was called
```

* 装饰器类

```
# 日志装饰器类
from functools import wraps
class logit(object):
    def __init__(self, logfile='out.log'):
        self.logfile = logfile
    def __call__(self, func):
        @wraps(func)
        def wrapped_function(*args, **kwargs):
            log_string = func.__name__ + " was called"
            print(log_string)
            # 打开logfile并写入
            with open(self.logfile, 'a') as opened_file:
                # 现在将日志打到指定的文件
                opened_file.write(log_string + '\n')
            # 现在，发送一个通知
            self.notify()
            return func(*args, **kwargs)
        return wrapped_function
    def notify(self):
        # logit只打日志，不做别的
        pass

@logit()
def myfunc1():
    pass
```

* 添加子类

```
class email_logit(logit):
    '''
    一个logit的实现版本，可以在函数调用时发送email给管理员
    '''
    def __init__(self, email='admin@myproject.com', *args, **kwargs):
        self.email = email
        super(email_logit, self).__init__(*args, **kwargs)
    def notify(self):
        # 发送一封email到self.email
        pass
```

从现在起，@email_logit将会和@logit产生同样的效果，但是在打日志的基础上，还会多发送一封邮件给管理员。

## 6.2 Function caching
```
#py3.2+ 新特性
from functools import lru_cache
#maxsize描述lru_cache最多缓存最近多少个返回值。

@lru_cache(maxsize=32)
def fib(n):
    if n < 2:
        return n
    return fib(n-1) + fib(n-2)

>>> print([fib(n) for n in range(10)])
#Output: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]


#清空缓存
fib.cache_clear()

```

```
#py2 手动通过装饰器实现该功能
from functools import wraps

def memoize(function):
    memo = {}
    @wraps(function)
    def wrapper(*args):
        try:
            return memo[args]
        except KeyError:
            rv = function(*args)
            memo[args] = rv
            return rv
    return wrapper

@memoize
def fibonacci(n):
    if n < 2: return n
    return fibonacci(n - 1) + fibonacci(n - 2)

fibonacci(25)
```

# 7、其他
## 7.1 One Liners
一些单行命令
```
* 起server
#Python 2
python -m SimpleHTTPServer
#Python 3
python -m http.server

* 漂亮的打印
from pprint import pprint
my_dict = {'name': 'Yasoob', 'age': 'undefined', 'personality': 'awesome'}
pprint(my_dict)
```

## 7.2 兼容Python2和Python3
* Future模块导入
导入__future__模块，它可以帮你在Python2中导入Python3的功能

```
print
# Output:
from __future__ import print_function
print(print)
# Output: <built-in function print>
```
* 重命名模块
```
try:
    import urllib.request as urllib_request  # for Python 3
except ImportError:
    import urllib2 as urllib_request  # for Python 2
```
* 通过引入包强制放弃过期的Python2内置功能
```
from future.builtins.disabled import *
apply()
# Output: NameError: obsolete Python 2 builtin apply is disabled
```
* 标准库向下兼容的外部支持
有一些包在非官方的支持下为Python2提供了Python3的功能
* enum pip install enum34
* singledispatch pip install singledispatch
* pathlib pip install pathlib

## 7.3 Python C extensions
* ctypes
ctypes模块提供了和C语言兼容的数据类型和函数来加载dll文件，因此在调用时不需对源文件做任何的修改。也正是如此奠定了这种方法的简单性。
1. 写代码
2. 编译为.so（windows为dll）
3. python中引入，调用编译后文件
```
from ctypes import *
...
```
* SWIG
开发人员必须编写一个额外的接口文件来作为SWIG(终端工具)的入口。
* Python/C API
需要以特定的方式来编写C代码以供Python去调用它。所有的Python对象都被表示为一种叫做PyObject的结构体，并且Python.h头文件中提供了各种操作它的函数。
[原文链接](https://www.bookstack.cn/read/interpy-zh/Introduction.md)