```
function serilizeURL(url){
	var rs=url.split("?")[1];
	var arr=rs.split("&");
	var json={};
	json["csp"] = url.split("?")[0].split(".csp")[0];
	for(var i=0;i<arr.length;i++){
		if(arr[i].indexOf("=")!=-1){
			json[arr[i].split("=")[0]]=arr[i].split("=")[1];
		}
		else{
			json[arr[i]]="undefined";
		}
	}
	return json;
}
```

```
function GetQueryObject() {
	var url = location.search;
	var theRequest = new Object();
	if (url.indexOf("?") != -1) {
		var str = url.substr(1);
		strs = str.split("&");
		for (var i = 0; i < strs.length; i++) {
		    var item = strs[i];
		    var key = item.split("=")[0];
		    var value = item.split("=")[1];
		    theRequest[key] = decodeURI(value);
		}
	}
	return theRequest;
}
```