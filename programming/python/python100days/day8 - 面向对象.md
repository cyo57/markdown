## 定义类

```python
'''
定义类
'''

class Student():
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def study(self, course_name):
        print(f'{self.name}正在学习{course_name}')
    
    def watch_movie(self):
        if  self.age < 18:
            print(f'{self.name}只能观看《熊出没》')
        else:
            print(f'{self.name}正在观看岛国爱情电影')

# 创建对象
def main():
    students = []
    students.append(Student('张三', 18))
    students[0].study('睡觉')

if __name__ == '__main__':
    main()
```

## 实例

```python
from time import sleep

class Clock():
    def __init__(self, h=0, m=0, s=0):    
        self.h = h
        self.m = m
        self.s = s
    
    def run(self):
        self.s += 1
        if self.s == 60:
            self.s = 0
            self.m += 1
        if self.m == 60:
            self.m = 0
            self.h += 1
        if self.h == 24:
            self.h = 0
    
    def show(self):
        print(f"{self.h:02d}:{self.m:02d}:{self.s:02d}")

def main():
    c1 = Clock(23, 59, 55)
    while 1:
        c1.run()
        c1.show()
        sleep(1)

if __name__ == "__main__":
    main()
```
