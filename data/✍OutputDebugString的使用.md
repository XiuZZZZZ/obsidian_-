

## 介绍

- 为什么使用：在客户电脑上未安装vs，可以用这个调试程序
- 如何使用：
	- 在程序的指定位置加上OutputDebugString(“输出内容”)
	- 使用工具DebugView工具
- 其他
	- 可以本地调试
	- 也可以远程调试
	- vs2019中的调试是不能使用的


## 准备程序

头文件添加

```
#include <Windows.h>
```

程序中添加

```
OutputDebugString("测试")
```

生成解决方案，打开exe文件

## 准备DebugView

添加过滤器

## 测试

运行程序

运行DebugView

查看结果

![[Pasted image 20230827153404.png]]