# Android Studio

阅读前建议学习CSS

## Hello World

### 更新

依次点击 **Help** > **Check for Update**（在 Mac 上，依次点击 **Android Studio** > **Check for Updates**）

- **Canary 版**：这些是前沿版本，大约每周更新一次

- **开发者版**：这些是精心挑选的 Canary 版本，已经过全面的内部测试。

- **Beta 版**：这些是基于稳定 Canary build 的候选版本，发布这些版本是为了在发布稳定版之前先收集反馈。

- **稳定版**：官方稳定版，可在 [developer.android.com/studio](https://developer.android.google.cn/studio?hl=zh-cn) 下载。

[test](#first-app)

### 创建应用

Android Studio 包含项目模板, 比如地图或者多个屏幕的应用

#### 创建空 Activity 项目

- Name
  应用名称

- Package Name
  包名

- Minimum SDK
  支持的最低 Android 版本

### 创建 Android 虚拟设备 (AVD)

运行虚拟机, 首先创建配置

**Tools > Device Manager**

### 在移动设备上调试APP (ADB)

需要安装 Google USB Driver 驱动, 在 Android Studio 中点击 Tools > SDK Manager > Android SDK > SDK Tools

移动设备打开 USB调试 选项

### 项目文件

Android Studio 会自动创建一个与应用同名的文件夹

Project 窗口默认默认以 **Android** 文件组织方式展示, 如果需要展示源目录应选择 **Project Source Files**

## Android 测试

就软件而言，测试是一种结构化方法，用于检查软件以确保其运行正常。自动化测试实际是一段代码，用于检查您所编写的另一段代码，确保其能够正常运行。

虽然手动测试几乎总是有它的一席之地，但 Android 中的测试通常可以自动执行. 使用 Kotlin 开发中将重点使用自动化测试

### 自动化测试

在 Android 项目中, acticity 位于 `main` 目录的一个子文件夹中, `src` 目录中, 存在 `test` 和 `androidTest` 目录. 此为测试代码的地方, 分别代表 Android 开发中的两种自动化测试: 单元测试和插桩测试

#### 查找单元测试代码

Android 视图

- **app** > **java** > **com.example.happybirthday (androidTest)** > **ExampleInstrumentedTest**

Project 视图

- **HappyBirthday** > **app** > **src** > **androidTest** > **java** > **com.example.happybirthday** > **ExampleInstrumentedTest**

## 创建应用

### 界面

- Project 窗口

- Design 视图

- Blueprint 视图

- Palette 窗口
  向应用添加 `Views` 的列表

- Component Tree 窗口
  屏幕视图的另一种呈现方式, 会列出所有视图

- Attributes 视图
  显示 `View` 的各个属性

### 更改界面

- app\
  包含大多数应用文件
  
  - res\
    用于各种资源, 例如图片和屏幕布局
    
    - layout\
      用于屏幕布局

#### 向布局添加 TextView

删除默认的 TextView, 点选按 Delete 即可

寻找**布局编辑器**左上角的 **Palette**, 包含 `Views` 的列表, 拖放到布局编辑器大致位置即可

此时 **Component Tree** 出现一个红色警告, 提示该视图未进行约束,下文以解决此问题

#### 放置 TextView

向顶部和左侧添加约束条件将带有外边距, 外边距制定了 `View` 距离所在的容器边缘的距离

在 **Attributes** 中找到 **Layout** 中的 **Constraint Widget**, 其中方形代表视图

点击 **+** 以添加约束条件, 数字代表外边距, 表示 `TextView` 到 容器(`ConstraintLayout`) 边缘的距离

#### 为 TextView 添加样式

更改大小, 文本颜色以及其他属性, 例如不同的字体

选择 **Component Tree** 的 `TextView`, 找到 **Attributes** 窗口的 **Common Attributes**, 然后修改属性, 比如 **fontFamily**, **textSize**, **textColor**

**dp** 是屏幕上距离的度量单位一样，**sp** 是字体大小的度量单位

### 添加 ImageView

添加图片资源 **View > Tool Window > Resource Manager**

确认图片位于应用的 **drawable** 文件夹中

如果在应用中展示图片, 需要有一个显示位置, 可以用 `ImageView` 显示图片. 接下来调整图片的位置和大小.

布局编辑器中拖动时, **Design** 视图中的粉色边框表示放置 `ImageView` 的屏幕边界. 但 `ConstraintLayout` 中的 `Views` 需要具有水平和垂直约束条件. 可在 **Attributes** 窗口中 **Constraint Widght **底部的 **+** 添加约束条件, 或者拖动小圆球.

此时图片居中, 但未占用整个屏幕.

在 **Constraint Widget** 的 **Constraints** 不分, 设置 **layout** 和 **scaleType**. 需要注意 **Component Tree** 的列表从上到下绘制, 即最后一个 View 将显示于最上层

### 采用规范的编码

根据上文操作添加 `TextViews` 时, Android Studio 出现警告标记.

#### 翻译

务必注意, 应用可能翻译成其他语言. 硬编码是字符串直接在应用代码中写入的字符串, 难以翻译成其他语言, 也很难在应用中重复使用. 我们可以通过"将字符串提取到资源文件中"解决此问题, 也就是将字符串放在一个文件中, 并未文件命名, 需要时使用名称即可. 即使更改字符串, 或翻译字符串, 名称仍然不变.

- 点击橙色警告, 查看 **Suggested Fix** 并 点击 **Fix**

- **Resource name** 表示字符串所用的名称
  
  **Resource value** 表示字符串实际的内容

- 在 **Extract Resource** 中修改 **Resource name** 为小写名称, 例如 **hello_world_text** , 其中单词用下划线分割.

- 打开 **strings.xml (app > res > values > strings.xml)**, 可查找到刚刚创建的字符串资源

#### 无障碍

关我屁事

## 创建交互式应用

拖入 `Button` 到 Design 视图中, 放置于 `TextView` 下方. 检查是否为 `CinstrainLayout` 的子级

为 `Button` 添加约束, 由于父级特性, 必须设置垂直和水平约束条件来确定 `Button` 的位置. 可以在 Design 中拖动白球连接至 `TextView`.

需要注意我们更改 `Button` 的 text 时, Android Studio 也会警告文字为硬编码, 同样可以点击警告确认自动修复

### text 属性的占位

在 `Common Attributes` 下, 有一个带有工具图标的 text 属性, 此属性为提供给开发者的 "tools text" , 用于在 Design 视图中预览效果, 实际编译不会生效.

### Activity

`Activity` 提供窗口供应用绘制界面, 通常占用整个屏幕. 顶级 Activity 通常称为 `MainActivity`, 由模板提供

导航到 **(app > java > com.example.roll > MainActivity.kt**) 预览代码

需要注意, 在 Android 应用中, 第一次运行时调用为 `MainActivity` 的 `onCreate()` 方法, 而不是 `main()` 函数.

### 启用自动导入

使用很多类时, 添加 `import` 将变得繁琐. 但 Android Studio 可以帮助您选择正确的导入

 **File > Other Settings > Setting New Project...**

展开 Other Setting > Auto Import. 在 Java 和 Kotlin 部分中, 确保选中 **Add unambiguous imports on the fly** 和 **Optimize imports on the fly (for current project)**

**优化导入**设置会指示 Android Studio 移除代码中未使用的导入

### 设置 Button 为可交互

指定点击某 Button 时, 屏幕底部出现 Toast

在调用 `setContentView()` 后, 添加代码到 `onCreate()` 方法中. `findViewById()` 方法在布局中找到 `Button`. `R.id.button` 是 `Button` 的资源ID, 为 Button 的唯一标识符, 代码将对象引用保存到变量中, 并不保存 `Button` 对象本身.

```kotlin
val rollButton: Button = findViewById(R.id.button)
```

> Android 会自动为应用中的资源分配 ID , 比如按钮具有资源 ID , 按钮文字也具有资源 ID.
> 
> 资源 ID 格式为 `R.<type>.<name>`, 则`R.string.roll`
> 
> View ID 格式为 `R.id.button`

添加代码后注意是否自动添加 `android.widget.Button`, 如果未启用自动导入, 则光标放置于高亮代码上, 然后按 Alt+Enter

将代码与 `Button` 关联, 便于点按时执行代码

```kotlin
rollButton.setOnClickListener {
    Toast.makeText(this, "Dice me", Toast.LENGTH_SHORT).show()
}
```

#### 点击按钮时更新 TextView

用户需要查看屏幕消息, 而不是临时 `Toast` 消息

查看 `activity_main.xml` 布局, 查看 `TextView` 的 id, 回到 `MainActivity.kt`

删除刚刚创建的 `Toast` 代码, 创建变量来存储对 `TextView` 的引用, 并修改 text 属性

```kotlin
val resultTextView: TextView = findViewById(R.id.textView)
resultTextView.text = "233"
```

### 添加 Button 逻辑

我们现在缺少的是投骰子的逻辑

#### 添加 Dice 类

在 `MainActivity` 类外 (后面) 添加代码

```kotlin
class Dice(val sides: Int){
    fun roll(): Int {
        return (1 .. sides).random()
    }
}
```

添加后 Android Studio 可能会显示提示, 将 `sides` 标记为 `private` 将仅允许 `Dice` 类访问 `sides` . 

之后我们再详细了解 `private` 与 `public` 变量

#### 创建 rollDice() 方法

在 `rollButton.setOnClickListener{}` 中, 只需要调用方法即可

我们将之前学习的逻辑代码放置于 `onCreate()` 外 (后面)

> Android Studio 在调用未定义的变量时, 会弹出提示, 可按照提示创建方法

在 `rollDice()` 方法中创建 `Dice` 的实例即可

### 良好的编码做法

#### 清理代码

可以将代码压缩为较短的代码块, 从而更加简洁, 例如

```kotlin
rollButton.setOnClickListener {
    rollDice()
}
```

因为代码只有一行, 我们压缩为

```kotlin
rollButton.setOnClickListener { rollDice() }
```

#### 格式化代码

在 `MainActivity.kt` 类中, 选中所有文本, 点击 **Code > Reformat Code**, 或者使用快捷键 `Ctrl+Alt+L`

#### 注释

通常为每个类和方法添加注释, 使用 `/**` 和 `**/`

### 总结

- 布局编辑器 添加 `Button`

- 修改 `MainActivity.kt` 添加交互行为

- `Toast` 作为临时方案, 验证操作正确

- `setOnClickListener()` 为 `Button` 设置点击监听器

- 运行中的应用, 通过界面元素调用方法更新屏幕

- 添加注释, 格式化并清理代码

### 了解

- [《使用 Kotlin 进行 Android 开发的基础知识》相关词汇](https://developer.android.com/courses/android-basics-kotlin/android-basics-kotlin-vocab?hl=zh-cn)

- [`Button`](https://developer.android.com/reference/android/widget/Button?hl=zh-cn) 类

- [`Toast`](https://developer.android.com/reference/android/widget/Toast?hl=zh-cn) 类

- [`TextView`](https://developer.android.com/reference/android/widget/TextView?hl=zh-cn) 类

### 练习

在应用中一次操作两个骰子, 并显示在不同的 TextViews 中

## 向应用添加图片

学习更新 `ImageView`, 在应用中更新屏幕上的图片

向添加 `TextView` 一样添加 `ImageView` 并添加约束条件, **Component Tree** 会看到一条警告, 提示 `ImageView` 的内容, 此处先不做处理, 之后将在 Koitlin 代码中进行更改

### 添加图片资源

在 Android Studio 中, 依次点击 **View > Tool Windows > Resource Manager** , 或者点击 **Project** 左侧的 **Resource Manager**

点击 **\+** , 选择 **Import Drawables**

> **重要提示**！- 您将能够在 Kotlin 代码中通过这些图片的资源 ID 查看它们：
> 
> - `R.drawable.dice_1`
> - `R.drawable.dice_2`
> - `R.drawable.dice_3`
> - `R.drawable.dice_4`
> - `R.drawable.dice_5`
> - `R.drawable.dice_6`

### 使用图片资源

#### 替换示例图片

- 在 **Disign Editor** 中 选择 `ImageView`.

- **Declared Attributes** 部分的 **Attributes** 中, 找到 **scrCompat**. 注意此属性为仅仅在AS的 **Design** 视图中看到的资源, 实际运行不会看到图片, 将其设置为骰子图片资源

哎呦我去, `ImageView` 居然占满了屏幕, 接下来调整 `ImageView` 的宽高, 确保不遮挡 `Button`

在**Constraints Widget** 下的 **Attributes** 窗口中, 默认 **layout_width** 和 **layout_height** 为 **wrap_content**. 这意味着宽高与原图片保持一致. 显然这对我们的屏幕来说太大了

- 手动修改 `ImageView` 的宽高, 并调整与其他元素的约束让视图更美观

> 使用密度无关像素 dp 作为尺寸单位, 可在不同分辨率的设备适当缩放图片

#### 点击按钮改变图片

首先将我们已删除的 `TextView` 代码也移除

- `MainActivity.kt` 中的 `R.id.textview` 已无法被识别

- 任何引用 `TextView` 的代码都移除

- 创建类型为 `ImageView` 的新变量, 并设置为与布局中的 `ImageView` 相同. 使用 `findViewById()` 方法, 将 `R.id.imageView` 作为参数.

```kotlin
val diceImage: ImageView = findViewById(R.id.imageView)
```

我们使用 `when`  使骰子图片对应

```kotlin
when (resultDice) {
    1 -> imageDice.setImageResource(R.drawable.dice_1)
    2 -> imageDice.setImageResource(R.drawable.dice_2)
    3 -> imageDice.setImageResource(R.drawable.dice_3)
    4 -> imageDice.setImageResource(R.drawable.dice_4)
    5 -> imageDice.setImageResource(R.drawable.dice_5)
    6 -> imageDice.setImageResource(R.drawable.dice_6)
}
```

但每种情况唯一改变的是值而已, 我们继续优化代码

```kotlin
val imageRes = when (resultDice) {
    1 -> R.drawable.dice_1
    2 -> R.drawable.dice_2
    3 -> R.drawable.dice_3
    4 -> R.drawable.dice_4
    5 -> R.drawable.dice_5
    else -> R.drawable.dice_6
}
```

`when` 表达式实际上可以返回一个值, 但 `when` 必须涵盖所有情况, 我们需要添加一个 `else` 分支即可

到此为止, 我们的应用已经可以使用, 但我们发现首次启动应用时 `ImageView` 没有更新, 是空白内容. 我们在 `onCreate` 方法中调用一次 `rollDice()` 以解决此问题

#### 添加代码注释

### [官方示例]([GitHub - google-developer-training/android-basics-kotlin-dice-roller-with-images-app-solution](https://github.com/google-developer-training/android-basics-kotlin-dice-roller-with-images-app-solution))

### 总结

学习使用 `setImageResource()` 更改 `ImageView`

使用 `if else` 和 `when`
