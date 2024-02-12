


## main

- 点
	- [[#基于tableExport.js，需要导入]]
- 修改
	- [[#新增方法，导出逻辑]]
	- [[#实现效果]]

## 实现效果

![[实现效果.png]]

## 新增方法，导出逻辑

```

function outToExcel(){
	// 创建一个 Excel Workbook 
	var excel = new ActiveXObject('Excel.Application');
	var workbook = excel.Workbooks.Add();
	var sheet = workbook.ActiveSheet;

	
	// 设置列的宽度为20
	var column = sheet.Columns("D");
	column.ColumnWidth = 20;
	
	// 设置第一行
	var column = getColumn()
	for (var i = 0; i < column.length; i++){
		var field = column[i].field
		var title = column[i].title
		sheet.Cells(1, i + 1).Value = title;
	}

	// 设置数据行
	var rows = $("#permitPatient").datagrid("getRows")
	for (var i =0; i < rows.length; i++){
		var row = rows[i]
		for (var j = 0; j < column.length; j++){
			var code = column[j].field
			sheet.Cells(i + 2, j + 1).Value = row[code];
		}
	}
	
	// 获取当前登录用户的个人文件夹路径
	var shell = new ActiveXObject("WScript.Shell");
	var userProfilePath = shell.ExpandEnvironmentStrings("%USERPROFILE%");
	var fileName = "出院召回审批"
	// 保存 Excel 文件到桌面
	var desktopPath = userProfilePath + "\\Desktop\\"+fileName+".xlsx";
	workbook.SaveAs(desktopPath);

	// 关闭 Excel
	excel.Quit();
}

function outToExcel(){
	// 创建一个 Excel Workbook 
	var excel = new ActiveXObject('Excel.Application');
	var workbook = excel.Workbooks.Add();
	var sheet = workbook.ActiveSheet;

	
	// 设置列的宽度为20
	var column = sheet.Columns("D");
	column.ColumnWidth = 20;
	
	// 设置第一行
	var column = getColumn()
	for (var i = 0; i < column.length; i++){
		var field = column[i].field
		var title = column[i].title
		sheet.Cells(1, i + 1).Value = title;
	}

	// 设置数据行
	var rows = $("#permitPatient").datagrid("getRows")
	for (var i =0; i < rows.length; i++){
		var row = rows[i]
		for (var j = 0; j < column.length; j++){
			var code = column[j].field
			sheet.Cells(i + 2, j + 1).Value = row[code];
		}
	}
	
	// 获取当前登录用户的个人文件夹路径
	var shell = new ActiveXObject("WScript.Shell");
	var userProfilePath = shell.ExpandEnvironmentStrings("%USERPROFILE%");
	var fileName = "出院召回审批"
	// 保存 Excel 文件到桌面
	var desktopPath = userProfilePath + "\\Desktop\\"+fileName+".xlsx";
	workbook.SaveAs(desktopPath);

	// 关闭 Excel
	excel.Quit();
}
```



## 基于tableExport.js，需要导入


