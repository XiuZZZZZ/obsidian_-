
http://192.168.99.125/imedical/web/csp/hisui.toNurMp.csp?EpisodeID=1526534&EmrCode=FA2715FF9A084866BB1942F8E9868B5E


```html
<csp:method name=OnPreHTTP
            arguments=""
            returntype=%Boolean>
    i ##Class(ext.websys.SessionEvents).SessionExpired() q 1 
    q 1
</csp:method>
<HTML lang="zh-CN">

<head>
    <meta http-equiv="Content-Type"
          content="text/html; charset=utf-8">
    <title>
        <EXTHEALTH:TRANSLATE id=title>##(%session.Get("TITLE"))##</EXTHEALTH:TRANSLATE>
    </title>
    <EXTHEALTH:HEAD></EXTHEALTH:HEAD>
    <HISUI/>
 
</head>

<body>



</body>

<script language="javascript">

	// 获取url中的请求参数，并将其解析为一个对象
	function getUrlParams() {
	  var search = location.search;

	  if (search === '') {
	    return {};
	  }

	  var params = {};
	  var paramStrs = search.substr(1).split('&');

	  for (var i = 0; i < paramStrs.length; i++) {
	    var paramStr = paramStrs[i];
	    var pair = paramStr.split('=');
	    params[pair[0]] = pair[1];
	  }

	  return params;
	}

	// 获取url中的请求参数对象
	var params = getUrlParams();



	var iframe = document.createElement('iframe');
	iframe.src = 'http://192.168.99.125/imedical/web/csp/DHCNURXTJCBNX.csp?EpisodeID='+params.EpisodeID+'&EmrCode='+params.EmrCode+'';
	iframe.width = '100%';
	iframe.height = '100%';
	iframe.frameBorder = '0'; // 去掉iframe的边框

	
	document.body.appendChild(iframe);
	
	
	var iframes = document.getElementsByTagName('iframe');
	console.log(iframes)
	var myIframe = iframes[0]; 

	if (myIframe) {
		
		var intervalId  = setInterval(function(){
			var myDocument = myIframe.contentDocument || myIframe.contentWindow.document;
			var div = myDocument.getElementById("div_ContainerElement_7")
			div.style.display = "none";
			if (div.style.display="none"){
				clearInterval(intervalId)
			}
		}, 1000);
	} else {
	  console.error('没有找到指定的iframe元素！');
	}
	
	
	

	/*
	//重定向
	var params = {
	  EpisodeID: "1526534",
	  EmrCode: "FA2715FF9A084866BB1942F8E9868B5E",
	};

	var queryString = Object.keys(params).map(function(key) {
	  return key + '=' + encodeURIComponent(params[key]);
	}).join('&');

	var xhr = new XMLHttpRequest();
	xhr.open('GET', 'http://192.168.99.125/imedical/web/csp/DHCNURXTJCBNX.csp?' + queryString);

	xhr.onreadystatechange = function() {
	  if (xhr.readyState === XMLHttpRequest.DONE) {
	    if (xhr.status === 200) {
	      console.log(xhr.responseText);
	    } else {
	      console.error('请求出错！状态码为：' + xhr.status);
	    }
	  }
	};

	xhr.send();

	//window.location.href = "http://192.168.99.125/imedical/web/csp/DHCNURXTJCBNX.csp?" + queryString;

	setInterval(function(){
		var div = document.getElementById("div_ContainerElement_7")
		div.style.display = "none";
	}, 1000);
	*/
	
</script>

</html>
```