# python123.io

## 入门挑战台2.0 Notes

### `zip()`

`zip()` 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的对象，这样做的好处是节约了不少的内存。

我们可以使用 list() 转换来输出列表。

```python
from string import ascii_lowercase
from string import ascii_uppercase

for lowercase, uppercase in zip(ascii_lowercase, ascii_uppercase):
    print(f"| {lowercase} - {uppercase} |")
```

## str[::]

python里面的索引的特征是包含起点，但是不包含结束的索引值，-1表示最后一个元素

[::-1] 顺序相反操作

```python
s = input()
print(s[::-1])
```

