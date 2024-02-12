

## main

全部医嘱的执行单打印按钮，需要只打印医嘱，不打印执行记录

- 过程
	- 找到根据医嘱打印，根据执行记录打印的地方
- 修改
	- 增加按钮，绑定新的方法
	- 新js，对应上面新的方法
	- 修改两个地方即可
		- 第一个地方
			- var printSheetType = window.config.printSheetType;
			- var printSheetType = true
		- 第二个地方
			- showExec: showExec
			- showExec: false