
- 业务
    - 公共打印入口js：scripts/nurse/nis/HandleMethod/OrderExcute.js function commonPrint
    - 置打印后台方法：Nur.NIS.Service.Base.OrderHandle setExecPrintFlag
    - 获取单据：Nur.NIS.Service.OrderExcute.SheetConfig GetAllSheet
    - 单据对应的表：CF_NUR_NIS.NurseSheetSet


# 打印逻辑

## 入口

https://172.210.0.1:1443/imedical/web/scripts/nurse/nis/HandleMethod/OrderExcute.js

sheetPrint

## 获得打印医嘱

```js
      printDateObject = queryDataNeedToBePrint(
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        vueComponent.execConfig.printSetting
      );
    // 主子医嘱执行记录ID
    var execIDNeedToBePrint = printDateObject.execIDs;
    // 主医嘱执行记录ID
    var execSeqIDNeedToBePrint = printDateObject.execSeqIDs;
```

## 调用打印程序--nis/NursePrintComm/SheetPrint.js

https://172.210.0.1:1443/imedical/web/scripts/nurse/nis/NursePrintComm/SheetPrint.js

```js
      if (window.SheetPrint) {
        sessionStorage.setItem("printStatusCode", 1);
        vueComponent.spinning = true;
        var printComm = true;
        window.resetSheetPrintResult();
        window.SheetPrint(
          _ref,
          vueComponent.utils,
          execIDNeedToBePrint.join("^"),
          execSeqIDNeedToBePrint.join("^"),
          episodeIDs.join("^"),
          sheetID,
          userName,
          searchRange
        );
        var getResultTimer = setInterval(getResultStatus, 500);
      }
```

## 获取打印配置

```js
      if (window.SheetPrint) {
        sessionStorage.setItem("printStatusCode", 1);
        vueComponent.spinning = true;
        var printComm = true;
        window.resetSheetPrintResult();
        window.SheetPrint(
          _ref,
          vueComponent.utils,
          execIDNeedToBePrint.join("^"),
          execSeqIDNeedToBePrint.join("^"),
          episodeIDs.join("^"),
          sheetID,
          userName,
          searchRange
        );
        var getResultTimer = setInterval(getResultStatus, 500);
      }
```

## 获取打印格式

```js
    // 获取打印格式配置
    var getPrintForm = new Promise(function(resolve) {
      ajax(orderExcuteApi.getSheetPrintForm, sheetID).then(function(result) {
        resolve(result);
      });
    });
```

## 获取所有的打印数据

Nur.NIS.Service.OrderExcute.SheetPrint

GetAllPrintData

```js
    var episodeIDChunks = splitChunk(episodeIDStr.split("^"), 5);
    var patData = {};
    var promiseArray = episodeIDChunks.map(function(chunk) {
      return ajax(
        orderExcuteApi.getSheetPrintData,
        chunk.join("^"),
        sheetID,
        "PAT"
      )
        .then(function(ret) {
          ret.forEach(function(patInfoObj) {
            patInfoObj["printUser"] = printUser;
            patInfoObj["printRange"] = printRange;
            patData[patInfoObj["episodeID"]] = patInfoObj;
          });
        })
        .catch(function(err) {
          printResult.statusCode = -3;
          printResult.msg = "获取患者信息失败" + " " + err;
  
```

## 并发所有请求

```js
    axios.all(promiseArray).then(function() {
      Promise.all([getBasicPrintSetting, getPrintForm]).then(function(
        promiseResult
      ) {
        var basicPrintSetting = promiseResult[0];
        if (typeof basicPrintSetting !== "object") {
          printResult.statusCode = -1;
          printResult.msg = "获取打印配置失败";
        }
        var printForm = promiseResult[1];
        if (typeof printForm !== "object") {
          printResult.statusCode = -2;
          printResult.msg = "获取打印格式失败";
        }
        // console.log(basicPrintSetting);
        // console.log(printForm);
        // console.log(patData);
        // console.log(orderPrintData);
        // 调用打印主程序方法
        main(
          oeoreSeqIDStr,
          oeoreIDStr,
          basicPrintSetting,
          printForm,
          patData,
          orderPrintData
        );
      });
    });
```

## 调用main方法

## 获得page

```js
    var allPage = createAllPage(
      sortList,
      patData,
      orderData,
      printConfig,
      printPaper,
      printForm,
      Section,
      TableHead,
      TableBody
    );
```

## 打印实例

## 打印TableBody

## 循环要打印的列

## 循环打印列的键值对数据

## 打印数据

## 打印下横线

# 护士执行打印--没有横线隔断

## 打印三条的数据

![[Pasted image 20230619224240.png]]

## 找到打印横线的位置

![[Pasted image 20230619224255.png]]

## 发现是配置