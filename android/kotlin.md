# Kotlin

阅读前需要确保有其他编程语言基础

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

## 安装Android Studio
