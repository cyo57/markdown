# Kotlin

阅读前建议有其他编程语言基础

## Hello World

### println()

Kotlin 语法接近于 js, 例如

```kotlin
fun main(args){
    println("Hello, World!")
    //print()同样是输出文本, 但不会换行
}
```

需要注意的是, **每个 Kotlin 程序都必须有`main`函数**

以上就是最简短的 Kotlin 程序

### 变量和循环

使用 `val` 关键字声明的变量只能设置一次。以后无法在程序中更改该变量的值。

使用 `var` 关键字声明可更改的变量

如果在字符串中输出变量, 则使用`${}` , 例如

**数据类型**

- `String` 字符串

- `Int` 整数

```kotlin
var name = "zeze"
println("Her name is ${name}")
```

### 循环

`repeat()` 语句, 比如需重复执行20次

```kotlin
fun main(){
    repeat(20){
        print()
    }
}
```

### 函数

`fun` 关键字用于标记函数, 紧跟函数的名称, 函数的可选输入 (即参数，需用圆括号括起来) 和大括号.

函数名称总是以小写的动词开头, 并且名称应描述函数的功能. 例如 `drawReallyCoolFancyBorder` 和 `printBirthdayMessage`.

函数定义中添加传递的参数

```kotlin
fun main() {
    var name = "Alice"
    var count = 18
    printMain(name, count)
}
fun printMain(name:String, count:Int){
    repeat(count){
        println(name)
    }
}
```

## 初遇 Android Studio

转至 [Android Studio](Android Studio.md)

## Kotlin 的类和对象

本节我们在 Android Studio 中制作一个随机**骰子程序**, 复习前文所学的

### 使用随机函数

前文我们学习了 `Int` 整数类型, `String` 字符串类型. 本节我们介绍 `IntRange`, 表示从起始到结束的整数范围. 本节我们用来表示骰子可能产生的值.

我们直接放上示例查看语法

```kotlin
val dice = 1 .. 6
// val diceRange: IntRange = 1 .. 6
```

可以看出, `1 .. 6` 是一个 Kotlin 范围, 表示数字 1 到 6, 类似于 Python3 中的 `range(1, 7)

我们调用 `.random()` 随机取其中元素, 并使用字符串格式表示法进行输出

```kotlin
fun main(){
    val diceRange = 1 .. 6
    val randomNum = diceRange.random()
    println("随机骰子点数为: ${randomNum}")
}
```

### 创建 Dice 类

上一小节的代码尽管可以运行, 但很与真是的骰子关联起, 我们将程序更贴近事物, 就能更容易让人理解.

"类"就好比蓝图规划, 他不是真正的房子, 而是关于如何建造房子的说明. 房子是根据蓝图建造的对象实例.

有了蓝图, 我们便可以快捷的建造多个"房子"

#### 定义类

在 `main()` 函数下添加, 先输入关键字 `class` , 后跟着类名称和大括号.

在类中, 可使用变量为类指定一个或多个属性. 例如骰子可以有多个面, 多个颜色 或 重量

```kotlin
fun main() {
    val myFirstDice = Dice()
    println(myFirstDice.sides)
}

class Dice {
    var sides = 6
}
```

#### 掷骰子

```kotlin
fun main(){
    var myDice = Dice()
    myDice.roll()
}

class Dice {
    var sides = 6
    fun roll(){
        var randomNumber = (1 .. 6).random()
        println(randomNumber)
    }
}
```

#### 返回值

上一小节代码虽然可以顺利运行, 但我们更常用返回值, 比如将 `roll()` 结果返回

```kotlin
val diceRoll = myDice,roll()
```

从前面几节我们了解到, 要使函数接收参数, 需要指定类型. 同样, 返回的数据也需要指定类型.

```kotlin
fun main() {
    var myDice = Dice()
    var diceNum = myDice.roll()
    println("你的${myDice.sides}面骰子点数为${diceNum}")
}

class Dice{
    var sides = 6
    fun roll(): Int {
        var randomNumber = (1 .. 6).random()
        return randomNumber
    }
}
```

#### 更改类的变量

```kotlin
fun main() {
    var myDice = Dice()
    myDice.sides = 12
    var diceNum = myDice.roll()
    println("你的${myDice.sides}面骰子点数为${diceNum}")
}

class Dice{
    var sides = 6
    fun roll(): Int {
        var randomNumber = (1 .. sides).random()
        return randomNumber
    }
}
```
