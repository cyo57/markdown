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

### 类的参数

并非所有骰子都是六个面, 我们如果不定义 `Dice` 的面数, 而在蓝图中设置为变量, 则不必更改现有 `Dice` 对象实例的属性.

```kotlin
fun main(){
    val myDice = Dice(66)
    println(myDice.roll())
}

class Dice(val sides: Int){
    fun roll(): Int{
        var randomNum = (1 .. sides).random()
        return randomNum
    }
}
```

### 规范的编码

保持尽量简洁, 我们可以去掉 `randomNum` 变量并直接返回数字

```kotlin
fun main(){
    val myDice = Dice(66)
    println(myDice.roll())
}

class Dice(val sides: Int){
    fun roll(): Int{
        return (1 .. sides).random()
    }
}
```

> 更改代码使其更简洁、高效或易于阅读和理解称为“重构”。这就像写一篇文章，先写一份包含所有信息的初稿，然后再对文字进行修改和提炼。

### 总结

- `IntRange` , 数列, `(1 .. 6)`

- `random()`, 在数列取随机

- 类, 类的函数, 类的属性, 类的接收参数

- `return`

### 练习

#### 为上文的 `Dice` 添加颜色属性

```kotlin
fun main(){
    val myDice = Dice(66, "blue")
    println(myDice.roll())
}

class Dice(val sides: Int, val color: String){
    fun roll(): String{
        return "面数为${sides}的${color}骰子点数为${(1 .. sides).random()}"
    }
}
```

#### 创建 `Coin` 类, 使其可翻转, 并抛出多个硬币

```kotlin
fun main(){
    var myCoin = Coin()
    var myCoin2 = Coin()
    println(myCoin.toss())
    println(myCoin2.toss())
}

class Coin{
    var status = 0
    fun toss(): Int{
        return (0..1).random()
    }
}
```

## 创建交互式应用

使用 Android Studio 的布局编辑器构建布局, 然后编写 Kotlin 代码处理点击 `Button` 时发生的情况

要点

- 添加 `Button`

- `Button` 行为

- `Activiry` 代码

- `Toast` 消息

- 应用运行时更新 `TextView`

### [转至Android Studio](Android Studio.md)

<a href="Android Studio.md">Test</a>
