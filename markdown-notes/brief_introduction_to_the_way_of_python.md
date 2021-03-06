# Python入坑指北

> Beautiful is better than ugly.
> Explicit is better than implicit.
> Simple is better than complex.
> Complex is better than complicated.
> Flat is better than nested.
> Sparse is better than dense.
> Readability counts.
> Special cases aren't special enough to break the rules.
> Although practicality beats purity.
> Errors should never pass silently.
> Unless explicitly silenced.
> In the face of ambiguity, refuse the temptation to guess.
> There should be one-- and preferably only one --obvious way to do it.
> Although that way may not be obvious at first unless you're Dutch.
> Now is better than never.
> Although never is often better than *right* now.
> If the implementation is hard to explain, it's a bad idea.
> If the implementation is easy to explain, it may be a good idea.
> Namespaces are one honking great idea -- let's do more of those!
>
> *The Zen of Python*, by Tim Peters

[TOC]

## 0 Motivation
比“怎么做”更重要的是“为什么这么做”，比“写完一串程序执行序列”更重要的是“对问题建立一个正确地抽象”。

假设读者对`C`有一定了解。

## 1 准备工作

### 安装环境

`PATH`

### `python` 与 `pip`

## 2 Helloworld

### 脚本式
创建一个后缀名为`.py`的文本文件`helloworld.py`，在其中写入:

```py
print("hello, world!")
```

在终端执行（如果你没有使用IDE的话）：

```bash
python helloworld.py
```

执行结果为：

```
hello, world!
```

> 就是这么简单！

### 交互式  

`python`提供了一个**交互式**shell；交互式，即“用户输入一行，解释器返回一行结果”的运行模式，对于简单的验证想法来说很方便。

在终端中输入`python`打开交互式shell，并执行语句：

```bash
[root@VM-16-15-centos ~]# python
Python 3.6.8 (default, May 21 2019, 23:51:36)
[GCC 8.2.1 20180905 (Red Hat 8.2.1-3)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print("hello, world!")
hello, world!
```

在后文中，为了表述方便，两种模式会混合使用。

## 3 变量与类型

### 定义和使用变量

在`python`中创建一个变量或给变量赋值非常简单：

```py
x = 1
x = "helloworld"
```

可以看到既不需要先声明变量，也不需要定义变量的类型，这体现了`python`作为一种**动态类型**语言的特性。“动态类型”，即“类型检查”是在运行时完成的，并且变量的类型是可以在运行中改变的。

初从`C`转来的同学可能会担心：“这不会把变量类型搞乱了吗？”但在这个问题上其实大可放心，“动态类型”不意味着不关心类型，在运行中的每一步，一个变量的类型都是可以确定的：

```py
x = 1
print(type(x))
x = "1"
print(type(x))
```

运行结果：

```
<class 'int'>
<class 'str'>
```

可以看到变量`x`始终具有确定的类型，只不过是从`int`变为了`str`。这使得函数传参时类型可确保，但是需要程序员来进行控制。


事实上，`python`是一种**强类型**语言。“强类型”，即严格的类型系统，比如偏向于拒绝*隐式类型转换*。在`python`中：

```py
x = 1
y = "1"
print(x + y)
```

运行结果为：

```bash
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

可以看到由于`int`和`str`之间不支持“相加”的操作，执行被中断并抛出了一个`TypeError`。

> 注意这里表达的微妙区别，是由于`int`与`str`之间没有“相加”操作才异常，而不是因为`int`与`str`类型不同而异常。见[鸭子类型](#duck-typing)。

相反，在作为一种**弱类型**语言的`C`中：

```c
#include <stdio.h>

int main()
{
    int x = 1;
    char y = '1';
    printf("%d\n", x + y);
   
    return 0;
}
```

运行结果：
```
50
```

由于*隐式类型转换*，`'1'`变为了其ascii码`49`，相加得到结果`50`。

> 在`C`中，“变量”的概念更等价于其实际机器中的值，而不是严格的类型控制。这种更加贴近硬件的特性是有优有劣的。

![编程语言类型](brief_introduction_to_the_way_of_python/type_in_languages.png)

动态类型给予了我们更强的表达能力。

### Duck-typing
鸭子类型（duck-typing）指一种编程风格，它并不限制对象类型，而是要求对象具有对应的方法。

> 如果一个东西看起来像鸭子，叫起来也像鸭子，那么肯定就是鸭子。

整数之间能够“相加”，是因为`int`具有`__add__`方法，它返回两个`int`对象的数学和：

```py
>>> 1 + 2
3
```

字符串直接能够“相加”，是因为`str`也具有`__add__`方法，它返回两个`str`对象的拼接：

```py
>>> 'a' + 'bc'
'abc'
```

`python`的许多函数被设计为接受鸭子类型的参数，比如`print()`， 基本上任意对象均可以不预加转换地直接传入`print()`进行打印：

```py
def foo():
    return 0

class Foo:
    pass

print(1, "f", foo, foo(), Foo, Foo())
```

执行结果为：

```bash
1 f <function foo at 0x00000144D2DAF040> 0 <class '__main__.Foo'> <__main__.Foo object at 0x00000144D3250A60>
```

上文打印了了多种类型的对象，分别为：整数、字符串、函数、函数的返回值、类、类的实例，均可以通过`print()`进行打印输出。

`print()`要求输入对象具有的是`__str__`或者`__repr__`方法。当一个对象被传入，`print()`调用`str()`将其转化为对应的内容并输出。

> `__str__`与`__repr__`目的略有区别，但大致都是为了将对象转化为字符串形式。

再一个例子比如用于打开文件的函数`open()`，其接受的参数`file`不需要规定明确的类型，只需要是`path-like object`。这意味着无论是直接传入一个表示路径的`str`或者`bytes`，还是传入一个具有`__fspath__`方法的对象，都可以被接受并正常工作。

`xxx-like object`是一个常见的`python`函数参数要求，他要求了对象的方法，而不是对象的类型。

### Type Hints

> 这是比较繁文缛节的一章。你可以先遍览全文，在落手实践前再阅读本章。

上文说到`python`并不会限制传入函数的参数类型和函数的返回类型，那么控制类型是否就完全没有必要了呢？也不尽然。

在代码规模不大的时候，我们希望写起来简单又能很快实现功能，完全依赖程序员的大脑确保类型无误，这是问题不大的。但是随着代码规模的扩大，确保参数类型成了比较难做（到没有BUG）的一件事，并且如果项目版本迭代需要代码重构的话，动态类型又会使得重构成为一个费尽心力的工作，因此`PEP 484 -- Type Hints`被提出了。

Type Hints是对函数或者变量的类型注解，在`python 3.5`被正式引入，它大致写起来是这样的：

```py
def add(x: int, y: int) -> int:
    return x + y

a: int = 100
b: int = 200

print(add(a, b))
```

可以看到带Type Hints的写法与之前相比只不过是在变量名或者参数名后增加了`:`，以及函数返回值增加了`->`，后面带上类型。Type Hints的使用也相当自由，不强制要求一定要有。如果没有的话，对应的参数类型要求会是`Any`。

> `Any`, `List`, `Tuple`, `Callable`等详细类型注解的写法参考库`typing`。
> 导入`from typing import List`以使用这些预定义的类型。

需要注意的是，正如其名，**Type Hints只是程序员对自己的约束（暗示）**，并不会真正在运行时检查类型。需要判断`arg`的类型可以使用

```py
if type(arg) is xxx:
    # do something
```

或者

```py
if isinstance(arg, xxx):
    # do something
```

> 后者与前者的区别是`isinstance()`会考虑继承关系，即子类的实例也是父类的实例。

那么有人又会有疑问，绕了这么大一圈，Type Hints又不在运行时检查，岂不是一点用都没有？并非。Type Hints的正确用法是在编写代码时IDE可以进行类型推断，如果编写代码时出现了类型错误会回报`error`或者`warning`，在这个意义上，程序员只需要根据报错信息检查代码，即可确保类型无误，**同时这么做也不会丧失动态语言的灵活性**。

> `Pycharm`甚至可以提供一些更酷的类型推断，比如上文中的函数如果不写Type Hints的话：
> ```py
> def add(x, y):
>     return x + y
> ```
> `Pycharm`会告诉你`x`的类型是`x: {__add__}`，即支持`__add__`方法。

### 可变对象与不可变对象

可变对象与不可变对象的区别是我们不得不讨论的一件麻烦事。

## 4 流程控制语句
### 条件判断：if
`python`的`if`语句格式如下：

```py
if [expression1]:
    # do something
elif [expression2]:
    # do something
else:
    # do something
```

`if`、`elif`、`else`均以`:`结尾，语句块内的语句需要缩进，多层迭代则多层缩进。

一个很好用的`python`语法糖是**链式比较**，比如你可以这么写，非常贴近自然语言：

```py
if 0 <= x < 100:
    # do something
```

它等价于：

```py
if 0 <= x and x < 100:
    # do something
```

### 循环：for

如果你需要将变量`i`从`0`到`99`迭代，或者精确的说，在$[0, 100)$内迭代，可以这么写：

```py
for i in range(100):
    # do something
```

`range(start, stop[, step])`产生从`start`开始，步长为`step`，小于`stop`的序列，其中`start`默认为`0`，`step`默认为`1`。因此，`range(100)`即等价于$0, 1, 2, \cdots, 99$这样的序列。

> 在`python`中，容器总是被设计为下标从`0`开始，方法的作用区间总是被设计为*左闭右开*。

> 在目前，了解如此使用`for ... in`循环的基本方法即可。受文字的线性所限，截止此章很难向读者解释清楚`in`是什么，`range()`又从何处而来。`python`的`for`是抽象层比比`C`的`for`更高的“循环”。事实上，**可迭代**具有更深层次的内涵，你可能会对[迭代器](#迭代器)感兴趣。


## 5 基本容器

数据类型千千万，但是绝大多数问题只需要简单的几种足以cover，`python`已经从语言的最底层为你做了最好的抽象，所以绝大多数时候，你不需要再重复造轮子了。

### List与Tuple

- **列表（list）**

    `list`是用于**顺序存放**元素的容器，是**可变的**。

    > 你可以用`C`中的数组来类比他，但其含义深远的多。

    使用`[]`来创建一个`list`：

    ```py
    a = [1, 2, 3]
    ```

    `list`是**泛型**的，你可以在其中存入任何对象，嵌套`list`也是可以的：

    ```py
    b = [1, "abc", [1, 2, 3], None]
    ```

    所谓顺序存放，即是可以通过下标（偏址）来实现高效地访问。`list`可以通过索引来访问其中的元素，其中，下标从`0`开始：

    > 在`python`中，容器总是被设计为下标从`0`开始，方法的作用区间总是被设计为*左闭右开*。

    ```py
    >>> print(b[1])
    abc
    >>> print(b[2])
    [1, 2, 3]
    ```

    使用`len()`方法获取`list`的长度（元素的个数）：
    ```py
    >>> b = [1, "abc", [1, 2, 3], None]
    >>> len(b)
    4
    ```

    使用`append()`方法来向`list`末尾增加元素：

    ```py
    >>> b.append(3.14)
    >>> b
    [1, 'abc', [1, 2, 3], None, 3.14]
    ```

    > 在末尾`append()`元素是非常高效的，`list`的底层实现超出了本文范围，但是你可以近似认为`append()`的时间复杂度是$O(1)$；与此不同的是，从`list`中间插入元素的`insert()`的时间复杂度是$O(N)$。

- **元组（tuple）**

    `tuple`是一种“类似”`list`的，可以按索引访问元素的容器，他们被设计的极为相似，区别是`tuple`是**不可变**的。

    使用`()`来创建一个`tuple`：

    ```py
    a = (1, 2, 3)
    ```

    使用`len()`方法获取`tuple`的长度（元素的个数）：

    ```py
    >>> len(a)
    3
    ```

    **不可变**意味着`tuple`内的元素一旦确定就不可改变，这带来了什么好处？这意味着`tuple`是**可哈希的**。

    很多时候，对象不可变对我们是一种很大的好处。

#### 切片

### Dict与Set

- **字典（dict）**

    `dict`是一种以`key-value`形式储存数据的结构，`key`可以为任意**可哈希**对象，`key`与`value`同样是**泛型**的。

    使用`{}`来创建一个`dict`：

    ```py
    a = {
        "key1": 1,
        100: None
    }
    ```

- **集合（set）**

### 迭代容器中的元素

`python`的基本容器均为设计为可迭代的。在`C`中，要遍历数组你可能会这么做：

```c
int a[] = {1, 2, 3};
int a_size = 3;

for (int i = 0; i < a_size; i++)
    printf("%d\n", a[i]);
```

可以看到需要的对象是数组本身及数组的长度，相应地写成`python`你可能会这么写：

```py
a = [1, 2, 3]

for i in range(len(a)):
    print(a[i])
```

但是这是非常不`pythonic`的做法，在`python`的抽象层次上，你可以直接迭代容器内的**元素**，而不是迭代容器内元素的**索引**。对于`list`，`tuple`你都可以：

```py
a = [1, 2, 3]
b = (4, 5, 6)

for x in a:
    print(x)

for x in b:
    print(x)
```

执行结果为：

```py
1
2
3
4
5
6
```

如果确实需要索引怎么办呢？可以使用`enumerate()`函数来同时迭代索引和元素：

```py
a = ["a", "b", "c"]

for i, x in enumerate(a):
    print(i, x)
```

执行结果为：

```py
0 a
1 b
2 c
```

对于`dict`的迭代略有不同，因为`dict`是`key-value`的结构，所以`for`实际上迭代的是`dict`的所有键值`dict.keys()`：

```py
a = {
    "a": 123,
    321: "b"
}

for key in a:
    print(key, a[key])
```

执行结果为：

```py
a 123
321 b
```

> **可迭代**具有更深层次的内涵，你可能会对[迭代器](#迭代器)感兴趣。

### 列表推导式

**列表推导式**（List Comprehensions）是`python`很棒的一个语法糖，它可以大大简化语句，而使得程序员注意力集中在更加关键的地方。

比如要生成基底为`0`到`9`的平方数，可以这么写：

```py
>>> a = [i ** 2 for i in range(10)]
>>> a
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```


它与以下代码完全等价：

```py
a = []
for i in range(10):
    a.append(i ** 2)
```

列表推导式中可以加判断以跳过一些元素，比如只要所有的偶数`i`：

```py
>>> a = [i ** 2 for i in range(10) if i % 2 == 0]
>>> a
[0, 4, 16, 36, 64]
```

前面的`i ** 2`可以为任意表达式，用于得到生成元素的值，所以你甚至可以这么写：

```py
>>> a = [x if (x := i**2) >= 16 else 0 for i in range(10) if i % 2 == 0]
>>> a
[0, 0, 16, 36, 64]
```

该列表生成式的含义是找出`10`以内所有偶数的平方，如果不大于等于`16`，则置`0`。

正如你所见，过度滥用语法糖可能会反而降低可读性，在实际编写代码时务必随时注意。

> `dict`、`set`也具有类似的推导式，请自行查阅文档，再次不作赘述。

## 6 函数与类

`python`是一种**多范式**的语言，对于过程式、函数式以及面向对象的写法均提供了支持。我们将[编程范式](#编程范式)的问题往后放一放，先从简单的点切入。

### 定义和使用函数

`python`通过`def`关键字来定义函数：

```py
def foo(arg1, arg2):
    # do something
```

`python`的函数可以返回多个值：

```py
>>> def origin():
...     return 0, 0
...
>>> x, y = origin()
>>> print(x, y)
0 0
```

### 函数的参数

函数的参数是`python`的优雅与强大的重要表现之一，`python`函数参数的多种写法几乎可以涵盖你的所有表达需求。

#### 位置参数

```py
def f(x, y):
    pass
```

这是最常见的函数定义形式，这里的`x`和`y`称为**位置参数（Positional Argument）**，是按照位置逐个填入的。

#### 位置参数：变长

`python`的函数支持变长参数，称为**任意实参列表（Arbitrary Argument Lists）**，在定义时未知参数数量，可以传入任意个参数。一个典型的例子是*求和函数*，我们希望对`list`内所有元素进行求和，但是预先不知道`list`内有多少元素：

```py
def f(*args):
    result = 0
    for x in args:
        result += x
    return result

print(f(1, 2, 3))
array = [1, 2, 3]
print(f(*array))
```

运行结果为：

```
6
6
```

可以看到在函数内传入的若干个参数被视作一个整体`args`，对`args`内元素进行处理，或者对`args`整体进行处理都可行。实质上`python`做的是将传入若干个参数打包为一个`tuple`，所以在调用`f(1, 2, 3)`时，实际上`args`就是`(1, 2, 3)`。

`*`操作符体现的是对`list`或者`tuple`的**解包**，它将一个整体拆分为若干个元素，所以在调用`f(*array)`的时候，实质就是传入了`f(1, 2, 3)`。

当与其他位置参数一同使用时，**变长参数应当总是处于其他位置参数的右边**，`python`将传入的参数逐个填入，其余的为变长参数。如：

```py
def f(x, *args):
    pass
```

变长或非变长的位置参数均为位置参数。

> 不一定要叫`*args`，变量名可以任取。

#### 关键字参数

很多时候我们希望函数的参数传入时能指定参数的名字，因为按照顺序一个个对实在是太麻烦了。`python`支持以`kwarg=value`形式传入的参数，称作**关键字参数（Keyword Arguments）**：

```py
def f(x, *args, y):
    print(x)
    print(args)
    print(y)

f("a", 1, 2, 3, y="b")
```

执行结果为:
```
a
(1, 2, 3)
b
```

在传参时，使用`y=value` 来为关键字参数`y`传入值。

> 关键字参数往往是与其他形式的参数以及默认值配合使用的。在参数特别复杂时，关键字参数是简化传参的最好办法。这里可能体现不出来。

当与位置参数同时使用时，**关键字参数应当总是处于位置参数的右边**。如果左边不含`*`的话，可以单独写一个`*`表示分隔（不占用参数位置）：

```py
def f(x, *, y):
    pass
```

#### 关键字参数：变长

关键字参数同样支持**解包**，区别为关键词参数传入时将打包为一个`dict`：

```py
def f(*args, **kwargs):
    pass
```

如上代码往往被设计用来使函数接受任意参数，其中`**kwargs`将传入的关键字参数打包为一个`dict`。在传入时，使用`**`将`dict`解包：

```py
def f(**kwargs):
    print(kwargs)

param = {
    "x": 1,
    "y": 2
}
f(**param)
```

执行结果为：

```
{'x': 1, 'y': 2}
```

#### 参数的默认值

参数可以具有**默认值（Default Argument Values）**：

```py
def f(x, y=100):
    pass
```

这样如果函数`f`在传入参数时仅传入了一个参数，传入参数将会作为形参`x`的值，`y`将取默认值`100`；如果传入两个参数时，则分别作为`x`和`y`。

特别需要注意，正如以上表述， 规定**具有默认值的位置参数应当总是处于不带默认值的位置参数的右边**，否则会导致混淆。

第二个需要注意的是，默认值应当只使用**不可变对象**，因为如果使用*可变对象*，函数多次调用，可能导致默认值本身发生变化。

> 一个默认值为可变参数导致函数功能与设想不一致的例子：
>
> ```py
> def f(a, L=[]):
>     L.append(a)
>     return L
> 
> print(f(1))
> print(f(2))
> print(f(3))
> ```
> 
> 输出结果为：
> 
> ```py
> [1]
> [1, 2]
> [1, 2, 3]
> ```
> 
> 不想在后续调用之间共享默认值时，应以如下方式编写函数：
> 
> ```py
> def f(a, L=None):
>     if L is None:
>         L = []
>     L.append(a)
>     return L
> ```

关键字参数同样支持默认值：

```py
def f(x, *, reverse=False):
    pass
```

这是一个常见的在`python`中配置函数特性的写法。在关键字参数中，具有默认值的关键字参数与不带默认值的关键字参数的位置没有要求，这也体现了`dict`的特性。

> 关于函数参数的更多特性，请参考[官方文档](https://docs.python.org/3/tutorial/controlflow.html#defining-functions)。

### 定义和使用类

通过`class`关键字来定义类：

```py
class Foo(object):
    name = "Foo"

    def __init__(self, x):
        self.x = x

    def add_one(self):
        self.x += 1

    def add(self, arg):
        self.x += arg

foo = Foo(0)
foo.add(100)
```

括号内为类`Foo`的父类，如果不写括号和里面的内容也是可以的，将默认继承`object`类。`object`是所有类的基类。

`name`是**类属性**。`__init__()`是类的构造函数，在实例化时被调用，并对自身附加**实例属性**`x`。

`__init__()`、`add_one()`与`add()`均为**实例方法**，实例方法的第一个参数必须为`self`，用以暗示实例方法作用于自身。但注意在调用的时候`self`是自动隐式传入的，只需要传入后面的参数即可。

`python`的类支持多重继承：

```py
class C(A, B):
    def __init__(self):
        super().__init__()
```

`super()`是用于调用父类方法的函数，推荐使用`super()`而不是具体的类名调用父类的方法，这降低了代码耦合。

`super()`能够使用MRO正确处理多重继承时的钻石继承问题，这也是使用`super()`的一个强有力的理由。

### 编程范式

不区分变量、函数、类、类的实例，它们均为**对象**。

你可以使用过程式的写法：





函数式编程指南



## 7 迭代器与生成器
### 可迭代
当我们从一个更高的角度来审视“迭代”这件事到底在做什么的时候，我们可以发现“迭代”所做的事情，不过是一直在试图获取“下一个值”。因此，支持`__next__`方法的对象我们*大约*可以称之为“可迭代”。

> 技术上来说，表述地不是很严谨。请往下阅读。

### 迭代器

**迭代器协议**要求对象具有以下两个方法，则可以称作`Iterator`：

- `__iter__`
- `__next__`

`__iter__`是允许对象配合`for ... in`语句使用的方法，返回迭代器本身。

`__next__`用于从容器中返回下一个对象，如果没有下一个对象，则应当引发`StopIteration`异常。



无限大的流

## 8 导入模块

正如你可能已经知道的，`python`因其丰富且强大的*第三方库*而著名。本文并不会介绍常见库`numpy`、`requests`等库的具体使用，而是介绍在`python`中正确导入模块的方式。

使用`import this`会在屏幕上打印目录前的“python之禅”

## 9 异常

EAFP

## 10 编码规范：PEP 8

## -1 参考文献

1. [Python 3.9.5 Documentation](https://docs.python.org/3/).
2. Michael T. Goodrich, Roberto Tamassia, Michael H. Goldwasser. 数据结构与算法Python语言实现.
