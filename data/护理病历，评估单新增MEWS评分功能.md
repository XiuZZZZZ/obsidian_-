

## 大纲

- 点
	- [[#增加统计分方法]]
	- [[#增加获取分值方法]]



## 增加统计分方法

```
var ItemArray = new Array()
//意识：1、清楚（0分）。2、对声音有反应（1分）。3、对疼痛有反应（2分）。4、无反应（3分）
ItemArray = ItemArray.concat([
	{"Item":"Item50_1","type":"=","score":0},
	{"Item":"Item50_2","type":"=","score":1},
	{"Item":"Item50_3","type":"=","score":2},
	{"Item":"Item50_4","type":"=","score":3}
])
//心率：1、＜40次/分（2分）、41-50次/分（1分）、51-100次/分（0分）、101-110次/分（1分）、111-130次/分（2分）、＞130次/分（3分）
ItemArray = ItemArray.concat([
	{"Item":"Item44","type":"<>",typeValue:"0,40","score":2},
	{"Item":"Item44","type":"<>",typeValue:"41,50","score":1},
	{"Item":"Item44","type":"<>",typeValue:"51,100","score":0},
	{"Item":"Item44","type":"<>",typeValue:"101,110","score":1},
	{"Item":"Item44","type":"<>",typeValue:"111,130","score":2},
	{"Item":"Item44","type":"<>",typeValue:"131,999","score":3}
])
//收缩压：＜70（3分）、70-80（2分）、81-100（1分）、101-199（0分）、≥200（2分）
ItemArray = ItemArray.concat([
	{"Item":"Item48","type":"<>",typeValue:"0,69","score":3},
	{"Item":"Item48","type":"<>",typeValue:"70,80","score":2},
	{"Item":"Item48","type":"<>",typeValue:"81,100","score":1},
	{"Item":"Item48","type":"<>",typeValue:"101,199","score":0},
	{"Item":"Item48","type":"<>",typeValue:"200,999","score":2}
])
//呼吸频率：＜9（2分），9-14（0分）、15-20（1分）、21-29（2分）、≥30（3分）
ItemArray = ItemArray.concat([
	{"Item":"Item46","type":"<>",typeValue:"0,8","score":2},
	{"Item":"Item46","type":"<>",typeValue:"9,14","score":0},
	{"Item":"Item46","type":"<>",typeValue:"15,20","score":1},
	{"Item":"Item46","type":"<>",typeValue:"21,29","score":2},
	{"Item":"Item46","type":"<>",typeValue:"30,999","score":3}
])
//体温：＜35（2分）、35-38.4（0分）、≥38.5（2分）
ItemArray = ItemArray.concat([
	{"Item":"Item40","type":"<>",typeValue:"0,34","score":2},
	{"Item":"Item40","type":"<>",typeValue:"35,38.4","score":0},
	{"Item":"Item40","type":"<>",typeValue:"38.5,999","score":2}
])
//SPO2：≥94（0分），90-93（1分），85-89（2分），小于等于84（3分）
ItemArray = ItemArray.concat([
	{"Item":"Item42","type":"<>",typeValue:"0,84","score":3},
	{"Item":"Item42","type":"<>",typeValue:"85,89","score":2},
	{"Item":"Item42","type":"<>",typeValue:"90,93","score":1},
	{"Item":"Item42","type":"<>",typeValue:"94,999","score":0}
])
	
	

function blindItemEvent(){
	//遍历上面的item，光标移开的时候，计算分值
	for (var i=0;i<ItemArray.length;i++){
		var ItemObj = ItemArray[i]
		var ItemCmp = Ext.getCmp(ItemObj["Item"])
        //给获取到的每个组件绑定事件
        if(ItemCmp) {
            ItemCmp.on('blur', function(){
				scoreAll()
			});
        }
	}

}

function scoreAll(){
	//debugger
	var score = 0
	for (var i=0;i<ItemArray.length;i++){
		var ItemObj = ItemArray[i]
		var ItemCmp = Ext.getCmp(ItemObj["Item"])
		if (ItemObj["Item"]=="Item40"){
		
			//debugger 
		}
		if(ItemObj.type == "<>") {
			var range = ItemObj.typeValue.split(',');
			var min = parseFloat(range[0]), max = parseFloat(range[1]);
			var value = ItemCmp.getValue()
			if (!isNaN(value)){
				var value = parseFloat(value)
				if(value >= min && value <= max) {
					var score = score + ItemObj.score;
					//根据score执行你的逻辑
				}
			}

		} else if(ItemObj.type == "=") {
			if(ItemCmp.checked==true){
				score = score + ItemObj.score
			}

		}
	}
	//debugger
	//总分
	var allScore = Ext.getCmp("Item70")
	allScore.setValue(score);
	//结果
	var scoreDesc = Ext.getCmp("Item71")
	if(score>=6){
		scoreDesc.setValue("Ⅰ级");
	}else if((score>=4)&&(score<=5)){
		scoreDesc.setValue("Ⅱ级");
	}else if((score>=2)&&(score<=3)){
		scoreDesc.setValue("Ⅲ级");
	}else if((score>=0)&&(score<=1)){
		scoreDesc.setValue("Ⅳ级");
	}
}
```

## 增加获取分值方法

```
/// ##class(Nur.Z.Need.MEWS).GetMews()
ClassMethod GetMews(EpisodeId){
	b
	s mewsResult = ""
	s code = "DHCNURPGD_Emprecheck"
	s codeU = $ZCONVERT(code,"U") 
	s rowId = ""
	f{
		s rowId = $O(^Nur.DHCMoudDataI("EmrCode"," "_codeU," "_EpisodeId,rowId),-1)
		q:rowId=""
		s obj = ##class(Nur.DHCMoudData).%OpenId(rowId)
		// 评分
		s score = $ZOBJPROPERTY(obj,"Item70")
		// 结果
		s scoreDesc = $ZOBJPROPERTY(obj,"Item71")
		s mewsResult = score_"^"_scoreDesc
		
	}
	q mewsResult
}
```