
- 框起来的代表是引用另一个文件的内容
- 下面是主要步骤

- 选定技术 tableExport.js
	- 环境准备
		- 下载js  https://github.com/hhurz/tableExport.jquery.plugin
	- 服务器上述js到服务器
	- csp中引入tableExport.js
- 初始化变量
	- 初始化一个变量  [[初始化导出excel的全局变量--2023-11-23]]
- 实现打印方法
	- 界面新增table，用于打印装载数据  [[shiftToExcelTable--2023-11-23]]
	- 新增一个js，并且导入  [[scripts/nurse/hisui/shiftToExcel.js--2023-11-23]]
	- 点击按钮，测试结果  [[shiftToExcel测试结果--2023-11-23]]


FileSaver.js  https://github.com/eligrey/FileSaver.js/releases/tag/v2.0.4
[[导出可选指定位置]]