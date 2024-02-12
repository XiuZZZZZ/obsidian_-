```
  function KFYDPrint(
    orders,
    check,
    printFlag,
    vueComponent,
    buttonData,
    queryDataNeedToBePrint
  ) {
    var $message = vueComponent.$message;
    var sheetCode = vueComponent.sheetCodeSelected;
    vueComponent.$i18n.setLocaleMessage(window.langcode, vueComponent.tranData);
    vueComponent.$i18n.locale = window.langcode;
    debugger
    var printDateObject = null;
    printDateObject = queryDataNeedToBePrint(false, false, false);
    var execIDNeedToBePrint = printDateObject.execIDs;
    var execSeqIDNeedToBePrint = printDateObject.execSeqIDs;
    var execIDsOfNoFilteringSameDate =
    printDateObject.execIDsOfNoFilteringSameDate;
    if (execIDNeedToBePrint.length === 0) {
      return vueComponent.$t("请选择需要打印的医嘱") + "!";
    }
    return function(updateOrder, _ref, info) {
	    var queryTypeCode = info.queryTypeCode || "";
        var kFYDPrintObj = function(){
            function Print(){
	            
				//获得病人和医嘱数据
				var infos = $cm({
				    ClassName:"Nur.Custom.NeedUnit.NeedUnit231023",
				    MethodName:"GetInfos",
				    OrderIdStr:execIDNeedToBePrint.join("^"),
				    Code:queryTypeCode
				},false);
	            
	            
	            
                var LODOP=getCLodop();
                LODOP.SET_PRINT_STYLE("FontSize",12)
                //遍历页码
                for (var i=0;i<infos.length;i++){
                    initNewPage(LODOP) //翻页，回归
                    initPrintIngParrLeft() //回归左点
                    initPrintIngParrTop() //回归顶点
                    PrintSmall(LODOP)

                    var Info = infos[i]
                    var printLine = Info.length
                    //遍历A5几个部分
                    for (var lineIndex = 0; lineIndex < printLine; lineIndex++){
                        var TypeCode = Info[lineIndex].TypeCode
                        //遍历code打印，病人信息--基本描述
                        if (TypeCode=="OneLineByCode"){
                            var PrintLineCodes = Info[lineIndex].PrintLineCodes
                            var PrintLineCodesLength = PrintLineCodes.length
                            for (var codeIndex = 0; codeIndex < PrintLineCodesLength; codeIndex++){
                                var text = PrintLineCodes[codeIndex].text
                                var value = PrintLineCodes[codeIndex].value
                                var code = PrintLineCodes[codeIndex].code
                                printIngParr.width = parseInt(PrintLineCodes[codeIndex].width)
                                PrintText2(LODOP,text+value,1)
                                printIngParr.left = printIngParr.left + parseInt(printIngParr.width)
                            }
                            initPrintIngParrLeft() //回归左侧
                            var unit = "mm"
                            LODOP.ADD_PRINT_LINE(
                                printIngParr.top+unit,
                                printIngParr.left+unit, 
                                printIngParr.top+unit, 
                                kFYDTemplate.small.width+printIngParr.left+unit,
                                0, 
                                1)
                            printIngParr.top += parseInt(Info[lineIndex].lineHeight)
                            
                            //打印下面线
                            var unit = "mm"
                            LODOP.ADD_PRINT_LINE(
                                printIngParr.top+unit,
                                printIngParr.left+unit, 
                                printIngParr.top+unit, 
                                kFYDTemplate.small.width+printIngParr.left+unit,
                                0, 
                                1)
                        }
                        //遍历医嘱打印
                        if (TypeCode=="OrderContent"){
                            var lines = Info[lineIndex].lines
                            for (var j = 0; j < lines.length; j++){
                                var lineFlag = lines[j].lineFlag
                                var value = lines[j].value
                                printIngParr.width = 120
                                PrintText2(LODOP,value,1)
                                //打印下面线
                                if (lineFlag){
                                    var unit = "mm"
                                    LODOP.ADD_PRINT_LINE(
                                        printIngParr.top+unit,
                                        printIngParr.left+unit, 
                                        printIngParr.top+unit, 
                                        kFYDTemplate.small.width+printIngParr.left+unit,
                                        0, 
                                        1)
                                }
                                printIngParr.top += parseInt(Info[lineIndex].lineHeight)
                                //打印下面线
                                if (lineFlag){
                                    var unit = "mm"
                                    LODOP.ADD_PRINT_LINE(
                                        printIngParr.top+unit,
                                        printIngParr.left+unit, 
                                        printIngParr.top+unit, 
                                        kFYDTemplate.small.width+printIngParr.left+unit,
                                        0, 
                                        1)
                                }

                            }
                            initPrintIngParrLeft() //回归左侧
                        }
                    }
                    printIngParr.pageIndex += 1
                }
                LODOP.PRINT(); 
            }
            var printStartParr = {
                "top":5,
                "left":5,
            }
            
            var printIngParr = {
                "page":0,
                "pageIndex":0,
                "top":5,
                "left":5,
                "width":20,
                "height":20,
                "fontSize":10,
                "blod":0,
            }
			var kFYDTemplate = {
			    "small":{
			        "height":130,
			        "width":90,
			    }
			}
            
            var printIntParrOrigin = JSON.parse(JSON.stringify(printIngParr))
            function initNewPage(LODOP){
                if(printIngParr.pageIndex!=0){
                    if(printIngParr.pageIndex%4==printIngParr.page){
                        LODOP.NEWPAGE()
                        printIngParr = JSON.parse(JSON.stringify(printIntParrOrigin))
                    }
                }
            }
            function initPrintIngParrLeft(){
                if ((printIngParr.pageIndex%2)==1){
                    printIngParr.left = printStartParr.left + 5 + kFYDTemplate.small.width*(printIngParr.pageIndex%2)
                }else{
                    printIngParr.left = printStartParr.left
                }
                
            }
            function initPrintIngParrTop(){
                //上面的
                if (((printIngParr.pageIndex)/4==0)||((printIngParr.pageIndex-1)/4==0)||((printIngParr.pageIndex+1)/4==0)){
                    printIngParr.top = printStartParr.top
                }else{
                    //下面的
                    printIngParr.top = printStartParr.top + 5 +kFYDTemplate.small.height
                }
                
            }

            function PrintText2(LODOP,Str,Top){
                var unit = "mm"
                LODOP.ADD_PRINT_TEXT(
                    printIngParr.top+Top+unit,
                    printIngParr.left+unit,
                    printIngParr.width+unit,
                    printIngParr.height+unit,
                    Str)
            }
            function PrintText(LODOP,Str){
                var unit = "mm"
                LODOP.ADD_PRINT_TEXT(
                    printIngParr.top+unit,
                    printIngParr.left+unit,
                    printIngParr.width+unit,
                    printIngParr.height+unit,
                    Str)
            }
            function PrintBottonLine(LODOP){
                
            }
            function PrintSmall(LODOP){
                var unit = "mm"
                LODOP.ADD_PRINT_RECT(
                    printIngParr.top+unit,
                    printIngParr.left+unit,
                    kFYDTemplate.small.width+unit,
                    kFYDTemplate.small.height+unit,0,0)
            }
            return {"Print":Print}
        }()
        kFYDPrintObj.Print()

		$message.error(vueComponent.$t("测试中..."));

    };
  }
```