## @property 装饰器

```python
class Person(object):

    def __init__(self, name, age):
        self._name = name
        self._age = age

    # 访问器 - getter方法
    @property
    def name(self):
        return self._name

    # 访问器 - getter方法
    @property
    def age(self):
        return self._age

    # 修改器 - setter方法
    @age.setter
    def age(self, age):
        self._age = age

    def play(self):
        if self._age <= 16:
            print('%s正在玩飞行棋.' % self._name)
        else:
            print('%s正在玩斗地主.' % self._name)


def main():
    person = Person('王大锤', 12)
    person.play()
    person.age = 22
    person.play()
    # person.name = '白元芳'  # AttributeError: can't set attribute


if __name__ == '__main__':
    main()
```

- getter方法
> @property
> 将一个方法转变为只读属性，意味着像访问属性一样去访问方法，而不是调用。例如 `person.name`

- setter 方法

> @value.setter
> 将一个方法转变为设置属性值的方法，通过类似属性赋值的方式来修改属性值。例如 `person.age = 22`

## __slots__

```python
class Person(object):

    # 限定Person对象只能绑定_name, _age和_gender属性
    __slots__ = ('_name', '_age', '_gender')

    def __init__(self, name, age):
        self._name = name
        self._age = age
```

- \_\_slots\_\_
  > 限定自定义类型的对象只能绑定某些属性，可以通过在类中定义__slots__变量来进行限定
  > 只对**当前类**的对象生效，对子类并不起任何作用
## 静态方法和类方法