



## main


- 过程
	- 界面
		- [[#增加window.config的变量]]
		- [[#增加按钮]]
		- [[#增加按钮绑定的方法]]
	- 界面
		- [[#增加csp]]
		- [[#增加js]]
			- 文本框，下拉框，保存等
	- 后台
		- [[#新增后台方法]]
			- 获取和保存


## 新增后台方法

```
Class Nur.Z.NeedUnit.Temperature Extends %RegisteredObject
{

/// 获取空白栏标题
/// ##class(Nur.Z.NeedUnit.Temperature).GetBlankTitle()
ClassMethod GetBlankTitle()
{
	//s ^MRC("OBITM",0,"Hospital",HospitalId,顺序,itemId)
	// 空白栏
	s json = ##class(%ListOfDataTypes).%New()
	s hospitalId = 2
	s index = "" f{
		s index = $o(^MRC("OBITM",0,"Hospital",hospitalId,index))
		q:index=""
		s itemId = "" f{
			s itemId = $o(^MRC("OBITM",0,"Hospital",hospitalId,index,itemId))
			q:itemId=""
			s code = $p(^MRC("OBITM",itemId),"^",1)
			continue:$p(^MRC("OBITM",itemId),"^",8)'="Y"
			s titleCodeU = $ZCONVERT(code_"Title","U")
			s titleItemId = $o(^MRC("OBITM",0,"Code",titleCodeU,""))
			s titleItemIndex = $p(^MRC("OBITM",titleItemId),"^",18)
			s sort("blankTitleItem",titleItemIndex)=titleItemId
		}
		
	}
	s index = "" f{
		s index = $o(sort("blankTitleItem",index))
		q:index=""
		s titleItemId = sort("blankTitleItem",index)
		s obj = ##class(%ArrayOfDataTypes).%New()
		d obj.SetAt(titleItemId,"id")
		d obj.SetAt($p(^MRC("OBITM",titleItemId),"^",2),"text")
		d json.Insert(obj)
		
	}
	b
	q json
}

/// 插入指定空白栏标题的值
/// ##class(Nur.Z.NeedUnit.Temperature).InsertBlankTitle(88,"1123")
ClassMethod InsertBlankTitle(itemId, value)
{
	s ^temp("InsertBlankTitle")=$lb(itemId, value)
	s retJson = ##class(%ArrayOfDataTypes).%New()
	d retJson.SetAt("0","status")
	d retJson.SetAt(value_"添加成功，请手动刷新下界面","info")
	//q retJson
	i $tr(value," ","")="" {
		d retJson.SetAt("-1","status")
		d retJson.SetAt("内容为空","info")
		q retJson
	}
	
	
	s obj = ##class(User.MRCObservationItem).%OpenId(itemId)
	s ItemValue = obj.ITMValues
	
	
	i ItemValue[value {
		d retJson.SetAt("-1","status")
		d retJson.SetAt("重复内容","info")
		q retJson
	}
	i value["@" {
		d retJson.SetAt("-1","status")
		d retJson.SetAt("存在特殊字符@","info")
		q retJson
	}
	s obj.ITMValues = obj.ITMValues_"@"_value
	d obj.%Save()
	q retJson
}

}

```

## 增加js

```

$(function(){

//初始化下拉
initBlankTitleCombobox()
//初始化输入框
initBlankTitleInput()
//初始化按钮
initBlankTitleBtn()
//关闭窗口
var ifReload = false
window.onbeforeunload = function() {
  if (window.opener && !window.opener.closed && ifReload) {
    window.opener.location.reload();
  }
};

function initBlankTitleBtn(){
	$('#blankTitleSaveBtn').click(function(){
		var itemId = $('#blankTitleCombobox').combobox('getValue');
		var value = $('#blankTitleInput').val()
		$cm({
		    ClassName:"Nur.Z.NeedUnit.Temperature",
		    MethodName:"InsertBlankTitle",
		    itemId:itemId,
		    value:value,
		},function(jsonData){
		    $.messager.alert("提示", jsonData.info, 'info');
		    if (jsonData.status==0){
				ifReload = true
			}
		});
	})
}


function initBlankTitleInput(){
	//$('#blankTitleInput').textbox();
	$('#blankTitleInput').addClass('hisui-validatebox textbox');
}

function initBlankTitleCombobox(){
	$('#blankTitleCombobox').combobox({
		valueField: 'id',
		textField: 'text',
	});
	$cm({
	    ClassName:"Nur.Z.NeedUnit.Temperature",
	    MethodName:"GetBlankTitle",
	},function(jsonData){
		$('#blankTitleCombobox').combobox('loadData', jsonData);
	});
}

})
```

## 增加csp

```
<csp:method name=OnPreHTTP
            arguments=""
            returntype=%Boolean>
    i ##Class(ext.websys.SessionEvents).SessionExpired() q 1 
    q 1
</csp:method>
<script language="cache"
        runat="SERVER">
        s EpisodeID = $G(%request.Data("EpisodeID", 1))
        s type = $G(%request.Data("type", 1))
        </script>
<HTML lang="zh-CN">

<head>
    <meta http-equiv="Content-Type"
          content="text/html; charset=utf-8">
    <title>
        <EXTHEALTH:TRANSLATE id=title>##(%session.Get("TITLE"))##</EXTHEALTH:TRANSLATE>
    </title>
    <EXTHEALTH:HEAD></EXTHEALTH:HEAD>
    <HISUI translate=1/>
    <script type="text/javascript" src="../scripts/nurse/hisui/editBlankTitle.js" charset=gbk></script>
    <STYLE type='text/css'>
	body {
		background-color: #fff;
		padding:0;
		margin:0;
	}
	body {
	  display: flex;
	  justify-content: center;
	  align-items: center;
	}

	table {
	  margin: 0 auto;
	}
	table {
	  border-spacing: 0 10px;
	}
	</STYLE>
</head>
<body>

	<table>
		<tr>
			<td>标题</td>
			<td><input id="blankTitleCombobox"></input></td>
			
		</tr>
		<tr>
			
			<td>内容</td>
			<td><input id="blankTitleInput"></input></td>
		</tr>
		<tr>
			<td></td>
			<td><button id="blankTitleSaveBtn" class="hisui-linkbutton">新增</button></td>
		</tr>
		
	</table>
</body>
</html>
```

## 增加按钮绑定的方法

```
    onWeiHuBtnClick(){
        // 获得窗口的垂直位置;
        const iTop = (window.screen.availHeight - 30 - 392) / 2;
        // 获得窗口的水平位置;
        const iLeft = (window.screen.availWidth - 10 - 830) / 2;
        const editBlankTitleSrc = window.config.editBlankTitleSrc;
        const editBlankTitleWidth = window.config.editBlankTitleWidth || 830;
        const editBlankTitleHeight = window.config.editBlankTitleHeight || 392;
        debugger;
        window.open(
          `${editBlankTitleSrc}?EpisodeID=${this.contextMenuEpisodeID}&type=F&TMENU`,
          "设置空白栏标题",
          `top=${iTop},left=${iLeft},width=${editBlankTitleWidth},height=${editBlankTitleHeight}`
        );
      
```
## 增加按钮


```
src\page\vitalSign\biz\batchEntry\businessNew.vue下
      <a-form-item>
        <a
          href="javascript:void(0)"
          class="l-btn l-btn-small printButton"
          @click="onWeiHuBtnClick()"
        >
          <span class="l-btn-left l-btn-icon-left">
            <span class="l-btn-text">{{ $t("维护空白栏") }}</span>
            <span
              class="l-btn-icon"
              :style="{
                background: `url(${publicPath}reset.png) center center no-repeat`
              }"
              >&nbsp;</span
            >
          </span>
          <p>{{ $t("维护空白栏！") }}</p>
        </a>
      </a-form-item>

```
## 增加window.config的变量

scripts/nurse/nis/config/index.js下

```
    dischargeSrc: "nur.hisui.discharge.csp",
    dischargeWidth: "846",
    dischargeHeight: "431",
    editBlankTitleSrc: "nur.hisui.editBlankTitle.csp",
    editBlankTitleWidth: "846",
    editBlankTitleHeight: "431",
```