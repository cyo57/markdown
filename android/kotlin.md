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

## 条件行为

### if 语句

```kotlin
fun main() {
    val num = 4
    if (num > 4) {
        println("The variable is greater than 4")
    } else if (num == 4) {
        println("The variabble is equal to 4")
    } else {
        println("The variable is less than 4")
    }
}
```

> 在 if-else 代码块中, 只能有一个 `if` 和 `else`, 但之间可以有任意数量的 `else if`
> 
> 常用的运算符, 例如 `<`, `<=`, `==`. 返回 `boolean` 类型

### when 语句

针对不同的结果很常见, `else if` 将会有n个. 为了提高可读性, Kotlin 提供了 `when` 语句

```kotlin
val luckyNumber = 4
when (rollResult) {
    luckyNumber -> println("Lucky")
    1 -> println("You rolled a 1.")
    2 -> println("You rolled a 2.")
}
```

输出内容与堆叠 `else if` 效果相同, 但可读性更高

## 类和继承

> - 类层次结构
> 
> - 子级或子类
> 
> - 父级, 父类, 基类
> 
> - 根类或顶级类
> 
> - 继承

### 继承

例如 Android 中有一个 `View` 类, 表示屏幕上一个矩形区域. `TextView` 类是 `View` 类的子类. `EditText` 和 `Button` 是 `TextView` 的子级.

### 创建抽象类

包含其所有子类通用的属性和函数. 如果属性的值及函数的实现情况未知, 则可以将其作为抽象类.

```kotlin
abstract class Dwelling(){
    abstract val buildingMaterial: String
    abstract val capacity: Int
}
```

我们添加建筑材料, 人数 属性

`abstract` 关键字则表示此处不会定义

所有房子都有一定的人数, 直接在父类中定义属性, 以便所有子类使用. 
添加住客人数的私有属性, 并设置为私有. (此处设置为 `var` , 因为人数可能会变化)

```kotlin
abstract class Dwelling(private var residents: Int){
    abstract val buildingMaterial: String
    abstract val capacity: Int
}
```

`residents` 属性使用 `private` 关键字进行了标记. 表示 `residents` 属性仅对此类可见(并且只能在此类中使用). 如果不指定则默认为 `public`

定义完成容量和人数后, 添加一个 `hasRoom()` 用于确定房间能否继续容纳住客

```kotlin
abstract class Dwelling(private var residents: Int){
    abstract val buildingMaterial: String
    abstract val capacity: Int
    
    fun hasRoom(): Boolean {
        return capacity > residents
    }
}
```

### 创建子类

在父类下, 创建一个 `SquareCabin` 的类. 并指明此类与基类相关, 表明拓展自 `Dwelling` 类. 因为 `SquareCabin` 将提供抽象部分的实现

注意传入父类需要的参数, 此处我们使用灵活的实现. 不要将 `residents` 声明为 `val,`, 因为在重复使用父类 `Dwelling` 中声明的属性

```kotlin
class SquareCabin(residents: Int) : Dwelling(residents)
```

> **注意**：使用这些类定义后，系统会在后台执行许多操作。
> 
> 在类标头中，您会看到 `class SquareCabin(residents: Int) ...`
> 
> 这实际上是 `class SquareCabin constructor(residents: Int) ...` 的简写形式
> 
> 当您通过某个类创建对象实例时，系统会调用 `constructor`。例如，当您调用 `SquareCabin(4)` 时，系统会调用 `SquareCabin` 的 `constructor`，以初始化对象实例。
> 
> `constructor` 会根据相应类中的所有信息（包括传入的参数）构建实例。当某个类从父级继承属性和函数时，`constructor` 会调用父类的 `constructor`，以完成对象实例的初始化。
> 
> 因此，当您使用 `SquareCabin(4)` 创建实例时，系统将执行 `SquareCabin` 的 `constructor`，由于继承关系，同时还会执行 `Dwelling` 构造函数。`Dwelling` 类定义指明其构造函数需要 `residents` 参数，因此，您会在 `SquareCabin` 类定义中看到传递给 `Dwelling` 构造函数的 `residents`。在之后的 Codelab 中，您将详细了解构造函数。

此时运行代码将会报错, 因为我们并未对抽象类的 `abstract` 变量提供值.

在 `Dwelling` 中定义的 `abstract` 变量, 子类中必须提供值. 使用 `override` 关键字表示属性在父类中定义的, 并且此类中将被替换

```kotlin
abstract class Dwelling(private var residents: Int) {
    abstract val buildingMaterial: String
    abstract val capacity: Int

    fun hasRoom(): Boolean {
       return residents < capacity
   }
}

class SquareCabin(residents: Int) : Dwelling(residents) {
    override val buildingMaterial = "Wood"
    override val capacity = 6
}
```

我们来创建实例, 因为 `SquareCabin` 为子类, 父级的函数将继承而来. 可使用父类中定义的方法.

```kotlin
fun main() {
    val mySquareCabin = SquareCabin(6)

    println("容量:${mySquareCabin.capacity}, 材质:${mySquareCabin.buildingMaterial}, 可否新增住户: ${mySquareCabin.hasRoom()}")
}

abstract class Dwelling(private var residents: Int) {
    abstract val buildingMaterial: String
    abstract val capacity: Int

    fun hasRoom(): Boolean {
       return residents < capacity
   }
}

class SquareCabin(residents: Int) : Dwelling(residents) {
    override val buildingMaterial = "Wood"
    override val capacity = 6
}
```

我们可以使用 `with` 简化代码. 避免重复输入

当使用某个类的特定实例, 并访问多个属性和函数时, 可使用 `with` 表明"对此实例执行以下操作"

```kotlin
with (mySquareCabin) {
    println("容量:${capacity}, 材质:${buildingMaterial}, 可否新增住户: ${hasRoom()}")
}
```

### 创建 RoundHut (茅草屋) 子类

```kotlin
fun main() {
    val mySquareCabin = SquareCabin(6)
    val roundHut = RoundHut(4)
    with (mySquareCabin) {
        println("容量:${capacity}, 材质:${buildingMaterial}, 可否新增住户: ${hasRoom()}")
    }
    with(roundHut) {
        println("容量:${capacity}, 材质:${buildingMaterial}, 可否新增住户: ${hasRoom()}")
    }
}

abstract class Dwelling(private var residents: Int) {
    abstract val buildingMaterial: String
    abstract val capacity: Int

    fun hasRoom(): Boolean {
       return residents < capacity
   }
}
class SquareCabin(residents: Int) : Dwelling(residents) {
    override val buildingMaterial = "Wood"
    override val capacity = 6
}
class RoundHut(residents: Int) : Dwelling(residents) {
    override val buildingMaterial = "Starw"
    override val capacity = 4
}
```

#### 创建 RoundTower 子类

同样是圆形, 此可作为 `RoundHut` 的子级

```kotlin
class RoundTower(residents: Int) : RoundHut(residents) {
    override val buildingMaterial = "Stone"
    override val capacity = 4
}
```

如果添加代码直接运行将会报错, 因为在 `Kotlin` 中, 类是最终层级, 无法继续子类. 只能从 `abstract` 类或者标记 `open` 关键字的类继承. 因此我们标记 `RoundHub` 类

```kotlin
open class RoundHut(residents: Int) : Dwelling(residents) {
   // ...
}
```

```kotlin
fun main() {
    val mySquareCabin = SquareCabin(6)
    val roundHut = RoundHut(2)
    val roundTower = RoundTower(4)
    with (mySquareCabin) {
        println("容量:${capacity}, 材质:${buildingMaterial}, 可否新增住户: ${hasRoom()}")
    }
    with(roundHut) {
        println("容量:${capacity}, 材质:${buildingMaterial}, 可否新增住户: ${hasRoom()}")
    }
    with(roundTower) {
        println("容量:${capacity}, 材质:${buildingMaterial}, 可否新增住户: ${hasRoom()}")
    }
}

abstract class Dwelling(private var residents: Int) {
    abstract val buildingMaterial: String
    abstract val capacity: Int

    fun hasRoom(): Boolean {
       return residents < capacity
   }
}
class SquareCabin(residents: Int) : Dwelling(residents) {
    override val buildingMaterial = "Wood"
    override val capacity = 6
}
class RoundHut(residents: Int) : Dwelling(residents) {
    override val buildingMaterial = "Starw"
    override val capacity = 4
}
class RoundTower(
    residents: Int,
    val floors: Int = 3) : RoundHut(residents) {
    // ...
    override val buildingMaterial = "Stone"
    override val capacity = 4 * floors
}
```

我们继续为 `RoundTower` 添加多个楼层. 如果有多个参数, 一行无法完全显示, 可划分为多行代码. 同时我们也为接收参数可添加默认值

```kotlin
class RoundTower(
    residents: Int,
    val floors: Int = 3) : RoundHut(residents) {
    // ...
    override val buildingMaterial = "Stone"
    override val capacity = 4 * floors
}
```
