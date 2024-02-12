
```

/*
*  所有的变量何方法基于  shiftToExcel
*
*/

function shiftToExcelMain(){
	//校验数据
	if (shiftToExcel.ClsNum==0){
		alert("数据有问题")
	}
	//把数据装载进table
	initShiftToExcelTable()
	noDomExportExcel()
}

function initShiftToExcelTable(){
	
	//获得班次columns描述  ==> shiftToExcelColumns
	
	
	//shiftToExcel.Data = [[[],[],[123],[123],[123]],[[],[],[123],[123],[123]],[[],[],[123],[123],[123]]]
	//获得班次columns的数据
	var shiftRows = $('#tableC').datagrid('getRows');
	var shiftRowsData = []
	var shiftClsData = []
	//好多项目
	for (var i=0;i<shiftToExcel.Columns.length;i++){
		var column = shiftToExcel.Columns[i]
		//三个班次,的项目
		for (var j=0;j<shiftRows.length;j++){

			var shiftRow = shiftRows[j]
			if (column.field=="shiftName") {
				shiftClsData[j] = shiftRow[column.field]
				continue;
			}
			num = shiftRow[column.field]
			value = column.title + " " + num 
			if (shiftRowsData[j]==undefined){
				shiftRowsData[j] = value
			}else{
				shiftRowsData[j] = shiftRowsData[j] + "  " + value
			}
		}
	}
	
	
	//设置第一行，的班次统计
	var cells = $('#shiftToExcelTable tr:first td');
	if (shiftToExcel.ClsNum==2){
		cells.eq(2).text(shiftRowsData[0]);
		cells.eq(3).text(shiftRowsData[1]);
	}else if (shiftToExcel.ClsNum==3){
		cells.eq(2).text(shiftRowsData[0]);
		cells.eq(3).text(shiftRowsData[1]);
		cells.eq(4).text(shiftRowsData[2]);
	}

	
	
	//设置第二行，数据
	var cells = $('#shiftToExcelTable tr:eq(1) td');

	cells.eq(0).text("患者信息");
	cells.eq(1).text("项目");
	if (shiftToExcel.ClsNum==2){
		cells.eq(2).text(shiftClsData[0]);
		cells.eq(3).text(shiftClsData[1]);
	}else if (shiftToExcel.ClsNum==3){
		cells.eq(2).text(shiftClsData[0]);
		cells.eq(3).text(shiftClsData[1]);
		cells.eq(4).text(shiftClsData[2]);
	}
	
	//获取患者数据
	var patientRows = $('#dg1').datagrid('getRows');

  // 将新行插入表格末尾
	for (var i = 0; i < patientRows.length; i++) {
		var cells = $('#shiftToExcelTable tr:eq(' + (i + 2) + ') td');
		// 姓名
		cells.eq(0).text(patientRows[i].name);
		// 项目
		cells.eq(1).text(patientRows[i].reason);
		// 白班
		cells.eq(2).text(patientRows[i].A);
		// 中班
		cells.eq(3).text(patientRows[i].P);
		// 晚班
		cells.eq(4).text(patientRows[i].N);
	}
	
	
}




//不操作dom导出Excel
function noDomExportExcel() {
	$("#shiftToExcelTable").show()
	$("#shiftToExcelTable").tableExport({type:"excel",escape:"false"});
	$("#shiftToExcelTable").hide()
}
```