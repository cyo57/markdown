# adb

内置于`android platform tools`

最常用的两条:

`adb devices` 打印已连接设备

`adb shell CMD` 执行shell命令

### pm

pm全称为Package Manager, 可执行应用的安装和查询应用和信息, 系统权限, 控制应用, 通常放置在 /System/bin

```adb
pm list permission-groups
pm list features
pm list users
pm enable [--user USER_ID] PACKAGE
pm disable [--user USER_ID] PACKAGE
pm disableuser [--user USER_ID] PACKAGE
......
```



# fastboot

