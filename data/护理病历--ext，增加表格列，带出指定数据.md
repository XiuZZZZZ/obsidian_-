

## main

- 点
	- 远程号：571767225  661093qq
	- 找到csp：[[#找到csp]] 
	- [[#找到界面表格生成的地方]]
	- 找到后台取上面数据方法： s findlisitems="web.DHCConusltReference.FindLisItem"  
	- 找到后台取下面数据方法：s findlisreport="web.DHCConusltReference.FindLisReport"
	- [[#增加上面列，界面]]
	- 增加上面列，后台  略，很简单







## 增加上面列，界面
scripts/nurse/message/dhcconsultreferencecom.js
增加条件，当为危急值的时候
```
function createpanel(id, t, ch, ci, cw, w, h, s) {
    var chs = ch.split("^");
    var cis = ci.split("^");
    var cws = cw.split("^");
    var length = cis.length;
    var fields = [];
    var colModel = [];
    for (var i = 0; i < length; i++) {
        fields[i] = cis[i];
        if (chs[i].indexOf("危机值")!=-1){
	        colModel[i] = {
	            header: (chs[i] || ""),
	            width: cws[i] ? parseInt(cws[i]) : 0,
	            fixed: true,
	            dataIndex: cis[i],
			    renderer: function(value){
				    
				    if (value=="1"){
					    return '<img src="../images/webemr/patientalert.gif" height="15" width="15" />';
					}else{
						return '';
					}
			        
			    }
	        };

	    }else{
	        colModel[i] = {
	            header: (chs[i] || ""),
	            width: cws[i] ? parseInt(cws[i]) : 0,
	            fixed: true,
	            dataIndex: cis[i]
	        };
	    }

    }
}
```

scripts/nurse/message/dhcconsultreferencelis.js
增加列数据，危急值
```
function part() {
    var ch1 = "就诊日期^就诊科室^医嘱名称^标本号^标本类型^危机值^送检日期^orw";
    var ci1 = "fa1^fa2^fa3^fa4^fa5^fa8^fa6^fa7";
    var cw1 = "75^110^175^90^90^50^100^0";
    var panel1 = createpanel("pa", "", ch1, ci1, cw1, 700, 250, true);
    var ch2 = "异常提示^描述^缩写^结果^单位^范围值^危机值^orw";
    var ci2 = "fb1^fb2^fb3^fb4^fb5^fb6^fb8^fa7";
    var cw1 = "60^145^110^90^90^100^50^0";
}
```

## 找到csp

dhcconsultreference.csp
dhcconsultreferencepacs.csp
dhcconsultreferencelis.csp


## 找到界面表格生成的地方



![[Pasted image 20240111093807.png]]