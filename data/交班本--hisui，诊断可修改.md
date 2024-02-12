

- 概述
- 过程
	- 增加持久化数据
		- 交班病人表，增加字段  [[#增加字段]]
	- 获取诊断
		- 界面放出来诊断 [[#界面放出来诊断]]
		- 添加get方法  [[#GetTheShiftEidDiagnosis]]
		- 修改明细中获取诊断的方法   [[#修改明细中获取诊断的代码行]]
	- 修改诊断
		- 增加按钮，并实现打开模态窗方法  [[#增加按钮]]
		- 增加  模态窗口  [[#模态窗口]]
		- 模态窗编辑内容后确认，提交修改  [[#提交修改]]
	- 修改打印绑定数据源
		- 


## 提交修改

```
function editDiagNosis(){
	debugger
	//当前病人
	var row = $('#dg1').datagrid('getSelected');
	var rowIndex = $('#dg1').datagrid('getRowIndex', row);
	var oldVal = row.diagnois
	var newVal = $("#diagNosis").val()
	if (oldVal!=newVal){
		var ret = tkMakeServerCall("Nur.Custom.NeedUnit.Shift","SetTheShiftEidDiagnosis","",row.mainRecordDR,row.clsSub,row.patSub,newVal)
		if (ret==1){
			row.diagnois = newVal
			$('#dg1').datagrid('updateRow', {
			  index: rowIndex,
			  row: row
			});
			$.messager.popover({ msg: "成功", type: "alert" });
		}else{
			$.messager.popover({ msg: "修改失败", type: "alert" });
		}
	}else{
		$.messager.popover({ msg: "数据未发生变化", type: "alert" });
	}
	
	
}
```
## 增加按钮

```
{
    iconCls: 'icon-write-order',
    text: '修改诊断',
    handler: function() {
        debugger
        //zhuxiaohang 1220
        //获得当前选中行
        var selectionsRows = $("#dg1").datagrid("getSelections")
        if ((selectionsRows.length > 1) || (selectionsRows.length == 0)) {
            $.messager.popover({
                msg: "请选中一条数据",
                type: "alert"
            });
        }
        //获得当前病人诊断
        var diagNosis = selectionsRows[0].diagnois
        $('#diagNosis').val(diagNosis)
        //弹出编辑窗口
        $("#modalEditDiagNosis").window('open');
    }
}
```

## 模态窗口

在modalAddPatient下面

```
<div id="modalAddPatient" class="hisui-dialog" title="新增" style="width:380px;top:250px;left:500px;padding:10px;" data-options="iconCls:'icon-add-item',resizable:false,modal:true,closed:true,onClose:closeAddPanel">  
    <input id="regNo" class="textbox" placeholder="回车检索登记号" onkeydown="keyup_submit(event);" style="margin-bottom:10px;"/>  
    <table id="addPatTable" style="margin:auto;height:72px;"></table>  
    <div style="text-align:center;margin-top:10px;">  
     <a href="#" class="hisui-linkbutton" data-options="iconCls:'icon-save'" onclick="addPatient()">添加</a>  
    </div>  
</div>  
<!--zhuxiaohang 1220-->  
<div id="modalEditDiagNosis" class="hisui-dialog" title="修改诊断" style="width:380px;top:250px;left:500px;padding:10px;" data-options="iconCls:'icon-add-item',resizable:false,modal:true,closed:true,onClose:closeAddPanel">  
    <textarea id="diagNosis" class="easyui-textbox" style="margin-bottom:10px;width:360px;height:100px;"></textarea>  
    <!--<input id="diagNosis" class="textbox" style="margin-bottom:10px;width:360px;height:100px;"/>-->  
    <div style="text-align:center;margin-top:10px;">  
     <a href="#" class="hisui-linkbutton" data-options="iconCls:'icon-save'" onclick="editDiagNosis()">修改</a>  
    </div>  
</div>
```

## 修改明细中获取诊断的代码行

```
///Nur.SHIFT.Service.ShiftBizV2  QueryShiftDetailList
.s diagnosis=##class(Nur.SHIFT.Service.ShiftBizBase).GetDiagByDoc(episodeID,3)  
.s diagnosis2=##class(Nur.Custom.NeedUnit.Shift).GetTheShiftEidDiagnosis("",mainRecordDR,clsSub,patSub)  
.i diagnosis2'="" s diagnosis = diagnosis2

```

## SetTheShiftEidDiagnosis
```
/// ##class(Nur.Custom.NeedUnit.Shift).SetTheShiftEidDiagnosis("",26699,2,18,"222")
ClassMethod SetTheShiftEidDiagnosis(ShiftEObj, ShiftRid, ShiftSid, ShiftEid, Diagnosis)
{
	s setRet = 1
	i $ISOBject(ShiftEObj){
		s ShiftEObj.SPRDIAGNOSIS = Diagnosis
	}else{
		s ShiftEObj = ##class(Nur.SHIFT.ShiftPatRecord).%OpenId(ShiftRid_"||"_ShiftSid_"||"_ShiftEid)
		s ShiftEObj.SPRDIAGNOSIS = Diagnosis
	}
	s setRet = ShiftEObj.%Save()
	i setRet'=1 {
		s setRet = "诊断保存失败"
	}
	q setRet
}
```

## GetTheShiftEidDiagnosis

```


/// ##class(Nur.Custom.NeedUnit.Shift).GetTheShiftEidDiagnosis("",26699,2,18)
ClassMethod GetTheShiftEidDiagnosis(ShiftEObj, ShiftRid, ShiftSid, ShiftEid)
{
	s SPRDIAGNOSIS = ""
	i $ISOBject(ShiftEObj){
		s SPRDIAGNOSIS = ShiftEObj.SPRDIAGNOSIS
	}else{
		s SPRDIAGNOSIS = $p(^Nur.SHIFT.ShiftRecord(ShiftRid,"C",ShiftSid,"P",ShiftEid),"^",13)
	}
	q SPRDIAGNOSIS
}
```

## 增加字段
Nur.SHIFT.ShiftPatRecord

/// 诊断  
Property SPRDIAGNOSIS As %String(MAXLEN = 500) [ SqlColumnNumber = 15, SqlFieldName = SPR_DIAGNOSIS ];





## 界面放出来诊断

```
                {
                    field: 'diagnois', title: '诊断', width: 150, align: 'left', editor: {
                        type: 'text'
                    }
	            }
```

![[Pasted image 20231218232051.png]]