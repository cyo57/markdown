# PySide6 & PyQT5

## 初识

- PyQT5
- PySide6
  - QT官方亲儿子
- **x64解释器发布的客户端无法在x86平台运行**

### *轻量化UI开发转至 Tkinter*

### 常见模块

- QtCore
  - 核心非GUI功能
- QtGui
  - 基本图形功能类
- QtWidgets
  - UI元素组建
  - **QApplication**
    - **.exec_()**
      - 接受用户输入事件, 分配给相应对象
  - QMainWindow
    - 主窗口
  - QPushButton
  - QPlainTextEdit
- QtMultimedia
  - 类库, 处理多媒体事件
- QtBluetooth
- QtNetwork
  - 网络编程类库
- QtPositioning
  - 位置信息
- Enginio
- QtWebSockets
- QtWebKit
- QtWebKitWidgets
- QtXml
  - 为SAX和DOM API的实现提供方法
- QtSvg
  - 显示矢量图形
- QtSql
  - 数据库对象的接口
- QtTest

## 环境搭建

### 安装PyQT5, PyQt5-tools 或者 PySide6

### 运行一个示例

- `QApplication`提供了整个图形界面的底层功能
  例如 初始化, 入口参数, 用户事件
  所以我们必须在任何界面控件对象创建前先创建它

- ```Python
  from PySide2.QtWidgets import QApplication, QMainWindow, QPushButton,  QPlainTextEdit
  app = QApplication([])
  
  window = QMainWindow()
  window.resize(500, 400)	#设置窗口大小
  window.move(300, 310)	#设置窗口位置, 屏幕左上角为0, 0
  window.setWindowTitle('薪资统计')
  
  textEdit = QPlainTextEdit(window)	#作为window的子窗口
  textEdit.setPlaceholderText("请输入薪资表")
  textEdit.move(10,25)	#相对于父窗口
  textEdit.resize(300,350)
  
  button = QPushButton('统计', window)
  button.move(380,80)
  
  window.show()
  
  app.exec_()	#等待用户输入
  ```

- 要在界面`创建控件`, 就需要在代码中`创建`这个`控件对应类`的一个`实例对象`

### signal 信号

- 在Qt中, 控件被操作时就会发出`信号` `signal`
  我们可以预先在代码中指定这个`signal`函数, 这个函数叫做`slot`

  ```python
  def handleCalc():
      print('我被点击了')
  ```

  指定如果button被点击, 需要`handleCal`, 比如`button.clicked.connect(handlCalc)`
  大致意思为, 让`handleCal`处理button点击事件

### 封装

- 将界面封装为类

- ```python
  from PyQt5.QtWidgets import QApplication, QMainWindow, QPushButton, QPlainTextEdit, QMessageBox
  
  class uiMain():
      def __init__(self):
          self.w = QMainWindow()
          self.w.resize(500,300)
          self.w.setWindowTitle('ZtCrack (预览版)')
  
          self.textEdit = QPlainTextEdit(self.w)
          self.textEdit.setPlaceholderText('哈哈')
          self.textEdit.resize(300, 30)
  
          self.button = QPushButton('Go', self.w)
          self.button.move(380, 80)
          self.button.clicked.connect(self.start)
  
      def start(self):
          mystr = self.textEdit.toPlainText()
          print(mystr)
          QMessageBox.about(self.w,
              '输出',
              mystr
      )
  
  app = QApplication([])
  uimain = uiMain()
  uimain.w.show()
  app.exec_()
  
  ```

- 

## 界面设计师 Qt Designer

### 界面

- 对象查看器

- 属性编辑器

## 动态加载UI

### PySide6

- ```Python
  from PySide6.QtWidgets import QApplication, QMainWindow, QPushButton, QPlainTextEdit, QMessageBox
  from PySide6.QtUiTools import QUiLoader
  
  class uiMain():
      def __init__(self):
          # 从文件加载UI
          # 控件对象也成为窗口对象的属性
          self.ui = QUiLoader().load('ui/main.ui')
          self.ui.startButton.clicked.connect(self.start)
  
      def start(self):
          mystr = 'self.textEdit.toPlainText()'
          QMessageBox.about(self.ui,
              '输出',
              mystr
      )
  
  app = QApplication([])
  uimain = uiMain()
  uimain.ui.show()
  app.exec_()
  
  ```

- 

