

# 修改

## 新增csp

```
<csp:method name=OnPreHTTP arguments="" returntype=%Boolean>
     i ##Class(websys.SessionEvents).SessionExpired() q 1
    q 1
</csp:method>
<html>
<head>

	<!-- Put your page Title here -->
	<title>	营养风险筛查表（NRS2002） </title>
	<script type="text/javascript" src="../scripts/nurse/hisui/NurMpToBed.js" charset=gbk></script> 
</head>

<body>
	<div id="main" style="width:100%;height:100%" >
	</div>
</body>

<server>
	s PatientListPage=""
	s EpisodeID = $G(%request.Data("EpisodeID", 1))
</server>
<script type="text/javascript">
	var EpisodeID = "#(EpisodeID)#"
	var iframeString = "<iframe id='iframetabDHCNURYYFXSCBXX' scrolling='auto' width='100%' height='100%' frameborder='0' src='DHCNURYYFXSCBXX.csp?EpisodeID="+EpisodeID+"&AuthorityFlag='></iframe>";
	document.getElementById("main").innerHTML += iframeString;
	
	
	
	//打开后，自动点击新建  ButtonElement_3
	var intervalId = setInterval(function() {
		debugger
		var element = window.frames[0].document.getElementById("ButtonElement_3")
		if (element) {
			element.click(); // 模拟点击操作
			clearInterval(intervalId); // 停止循环
		}
	}, 500); // 每隔1秒进行一次检查
	
</script>

</html>

```

## 维护右键菜单

![[Pasted image 20231120161046.png]]

## 效果预览
![[Pasted image 20231120160958.png]]