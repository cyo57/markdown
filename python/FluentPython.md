# Fluent Python Note

## 前言

在学校里，或者是入门书籍上，教授者往往有意避免介绍这门语言所独有的功能。

另外，人们总是倾向于追寻自己熟悉的东西。受到其他语言的影响，你大概能猜到Python支持正则表达式，然后查阅文档。但如果你从未接触过元组拆包 (tuple unpacking)，也没听说过描述符 (descriptor)，那么你估计不会搜索，然后永远失去了使用Python独有的功能的机会。

本书的内容会有些“超纲”，如果在学习Python的过程中过早接触本书内容，你可能会误以为所有的Python脚本都需要使用特殊方法和元编程技巧。不成熟的抽象和过早的优化一样，都会坏事。

### 内容编排

> 建议你不要跳过第一章。本书第一到第五部分可以分别看作五本独立的书

第一部分 数据结构

- 说明为什么特殊方法如 `__repr__`是所有类型的对象在行为上保持一致，特殊方法各章深入讲解。

- 介绍各种容器类型，还有标准库中的高级类构建起：具名元组工厂和@dataclass 装饰器

第二部分 函数即对象

- 如何利用闭包 (closure) 实现函数装饰器。这一部分涉及Python中可调用函数的概念、函数属性、内省、参数注解

第三部分 类和协议

- 重点在自己动手构建类上，和第五章介绍的类构建器截然不同。这一部分说明如何自己动手构建容器、抽象基类和协议，一级如何处理多重继承、如何在必要时实现运算符重载

第四部分 控制流

- 除传统控制流以外，本章包含生成器、上下文管理器、协程，和新增的`yield from`

第五部分 元编程

- 动态创建带属性的类，然后从property 机制入手。整个第五章一步步实现一个字段验证器，过程中会遇到一些不易察觉的问题，由此引出最后一章的高级工具

- 类装饰器和元类 (metaclass)

## 数据结构

**魔法方法和下划线**

特殊方法用行话说叫做**魔法方法**，需要把一个特殊方法`__getitem__`说出来时，一般说 "dunder-getitem", "dunder" 表示"前后双下划线"，因此特殊方法也叫"双下划线方法"

### Python风格的纸牌

```python
import collections  # 导入collections模块，用于创建namedtuple

# 使用namedtuple创建一张扑克牌的结构，包括'rank'（点数）和'suit'（花色）
Card = collections.namedtuple('card', ['rank', 'suit'])

class FrenchDeck:
    # 定义点数（ranks）和花色（suits）的列表
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()

    def __init__(self) -> None:
        # 初始化法式扑克牌，生成一整副牌的列表
        self._cards = [Card(rank, suit) for suit in self.suits
                                        for rank in self.ranks]

    def __len__(self):
        # 定义__len__方法，返回牌堆中的牌数
        return len(self._cards)

    def __getitem__(self, position):
        # 定义__getitem__方法，根据索引位置返回相应的扑克牌
        return self._cards[position]

deck = FrenchDeck()
# 返回一摞牌多少张
len(deck)
```

首先用`collections.namedtuple`构建了一个简单的类，表示单张纸牌。此方法构建只有属性而没有自定义方法的类对象，此示例中用于表示一落排中的每张纸牌，例如

```python
>>> my_card = Card('7', 'diamonds')
>>> my_card
Card(rank='7', suit='diamonds')
```

如果想随机抽取一张纸牌，需要定义一个方法吗？不需要，因为Python已经提供了从序列中随机获取一项的函数 `random.choice`

```python
choice(deck)
```

可以看到，通过特殊方法利用Python数据模型，有两个优点：

- 类的用户不需要记住标准操作的方法名称（使用`.size()`还是`.length()`还是其他？）

- 充分利用Python标准库，例如`random.choice`，无需重复造轮子

### 特殊方法使用

> 特殊方法供Python解释器调用，而不是你自己。也就是说，没有`my__object.__len__()`这种写法，正确的写法是`len(my_object)`。如果`my_object`是用户定义的类的实例，Python将调用你的`__len__()`

#### 模拟数值类型

实现一个二维向量类

```python
>>> v1 = Vector(2, 4)
>>> v2 = Vector(2, 1)
>>> v1 + v2
Vector(4, 5)
```

`+`运算符的结果是一个新的`Vector`对象，我们的API也使用 `abs` 函数计算向量的模

```python
>>> v = Vector(3, 4)
>>> abs(v)
5.0

>>> v * 3
Vector(9, 12)

>>> abs(v * 3)
15.0
```

**示例** (使用`__repr__`, `__abs__`, `__add__`, `__mul__` 等特殊方法实现运算)

```python
import math


class Vector:
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    def __repr__(self):
        return f'Vector({self.x!r}, {self.y!r})'

    def __abs__(self):
        # 向量的长度或模
        return math.hypot(self.x, self.y)

    def __add__(self, other):
        x = self.x + other.x
        y = self.y + other.y
        return Vector(x, y)

    def __bool__(self):
        return bool(abs(self))
```

示例中的方法创建并返回新的 `Vector` 实例，没有修改运算对象，只是读取 `self` 或 `other`，这是中缀运算符的预期行为，即创建新对象，不修改运算对象。

#### 字符串表示形式

`__repr__`供内置函数`repr`调用，获取对象的字符串表示形式。

`__repr__`方法返回的字符串应当没有歧义，如果可能，最好和源码保持一致，方便重新创建所表示的对象。

#### 自定义类型的布尔

为了确定 x 表示的值为真或假，Python调用 `bool(x)`，返回 True 或 False

默认情况下，`bool(x)`调用 `x.__bool__()`，以后者返回结果为准。如果没有实现 `__bool__()`，Python则调用 `x.__len__()`，如果返回0则`bool()`返回False，否则返回True

### 特殊方法

### len为什么不是方法

> 当 x 是内置类型的实例时，`len(x)`运行速度非常快。
> 
> 之所以len不作为方法调用，是因为它经过了特殊处理，被当作Python数据模型的一部分，就像abs函数一样。
> 
> 但是借助特殊方法`__len__`·，也可以让len适用于自定义对象，这是一种相对公平的折中方案，满足了内置对象对速度的要求，又保证了语言的一致性。

计算s中有多少个x，写作`s.count(x)` (s是某种序列)

### 小结

借助特殊方法，自定义对象的行为可以像内置类型一样，写出更具表现力的代码，符合社区认可的Python风格

### 内置序列类型

## 函数即对象

## 类和协议
