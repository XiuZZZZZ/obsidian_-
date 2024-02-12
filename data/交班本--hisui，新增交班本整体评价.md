
# 过程



## 指定地方新增文本框

```
	<div id="modalSign" class="hisui-dialog" title="交接班" style="width:800px;height:500px;top:110px;padding:10px 10px 0;" data-options="iconCls:'icon-w-switch',resizable:false,modal:true,closed:true,buttons:[{
		text:'保存',
		handler:function(){MagicSave()}
	}]">   
	    <div style="margin:0 auto">
	    	<label style="padding-right:10px;">交班护士</label><select id="nurseBox" style="width:200px;"></select>
	    	<label style="padding-right:10px;">接班护士</label><select id="nurseBox2" style="width:200px;"></select>
	    </div>
	    <div style="margin:0 auto">
	    	<label style="padding-right:10px;">交班内容</label>
	    	<textarea id="myTextArea" class="hisui-textbox" style="width: 770px; height: 320px;"></textarea>
	    </div>
	    <div  style="margin:10px auto">
	    	
	    </div>
	</div>
```

## 保存修改

- 增加后台请求
- MagicSave

Content:$('#myTextArea').textbox('getValue')

## 后台修改

### 业务表新增字段


Property SCRContent As %String(MAXLEN = 500) [ SqlColumnNumber = 13, SqlFieldName = SCR_Content ];

### 获取方法

```
function openModal(modalID)
{
	//获取交班内容
	$cm({
        	ClassName: "Nur.SHIFT.Service.ShiftBizV2",
            MethodName: "GetShiftRecordSidContent",
            WardID:wardID,
            TJDate:tjDate, //2023-11-11
            ShiftType:shiftType
        },function (data) {
	        $('#myTextArea').val(data)
        })
	
	$("#"+modalID).window('open');
}
```

```
	//获取交班内容
	$cm({
        	ClassName: "Nur.SHIFT.Service.ShiftBizV2",
            MethodName: "GetShiftRecordSidContent",
            WardID:wardID,
            TJDate:tjDate, //2023-11-11
            ShiftType:shiftType
        },function (data) {
	        $('#myTextArea').val(data)
        })
	
	$("#"+modalID).window('open');
```