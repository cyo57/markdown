# Android Studio

阅读前建议学习CSS

## Hello World

### 更新

依次点击 **Help** > **Check for Update**（在 Mac 上，依次点击 **Android Studio** > **Check for Updates**）

- **Canary 版**：这些是前沿版本，大约每周更新一次

- **开发者版**：这些是精心挑选的 Canary 版本，已经过全面的内部测试。

- **Beta 版**：这些是基于稳定 Canary build 的候选版本，发布这些版本是为了在发布稳定版之前先收集反馈。

- **稳定版**：官方稳定版，可在 [developer.android.com/studio](https://developer.android.google.cn/studio?hl=zh-cn) 下载。

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
