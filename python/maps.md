# Python速查地图

## 常用库
### requests

- urllib的封装

### selenium

- Chrome Driver

### sys

- sys.argv

### argparse

- 命令行接口

- ```python
  import argparse
  
  help_info = '帮助文档'
  parser = argparse.ArgumentParser(description=help_info)
  parser.add_argument('-s1', type=int, default=1,help='x.x.!.x')
  parser.add_argument('-s2', type=int, required=True, help='x.x.x.!') #required设置是否必须
  args = parser.parse_args()
  
  print(args.s1, args.s2)
  ```

- 



## 技巧类

### 运行时传入参数

- import sys
  - `python test.py 1,2,3
- import argparse



## 还没想好

