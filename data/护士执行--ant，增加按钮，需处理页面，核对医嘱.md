

## main


- 过程说明
	- 类保存的时候（%Save），返回1代表成功
- 修改
	- 实体类
		- [[#新增类--增加实体，增加索引]]
		- [[#新增置数据方法]]
	- 前端
		- [[#新增api]]
		- [[#新增js对应方法]]
	- 测试


## 测试


![[GW~IS}C}WYFU1[HJ2]UBR4G 1.png]]


## 新增js对应方法

```
function checkOrder(orders, check, printFlag, vueComponent) {
	var orderIDNeedToBeDispose = [];
	var $message = vueComponent.$message;
	vueComponent.$i18n.setLocaleMessage(window.langcode, vueComponent.tranData);
	vueComponent.$i18n.locale = window.langcode;
	// var $t = vueComponent.$t;
	orders.forEach(function(order) {
		if (order.ch![[GW~IS}C}WYFU1[HJ2]UBR4G.png]]eck) {
			orderIDNeedToBeDispose.push(order.ID);
			if (order.sameLabNoOrders && order.sameLabNoOrders.length !== 0) {
				order.sameLabNoOrders.forEach(function(sameLabNoOrder) {
					orderIDNeedToBeDispose.push(sameLabNoOrder.ID);
				});
			}
		}
	});
	if (orderIDNeedToBeDispose.length === 0) {
	  return vueComponent.$t("请选择需要处理的医嘱") + "!";
	}
    return function(updateOrder, _ref, info) {
		var ajax = _ref.ajax;
		var axios = _ref.axios;
		var orderHandleApi = _ref.orderHandleApi;
		var type = info.type || "F";
		var notes = info.changeReasonDr || "";
		var date = info.date || "";
		var time = info.time || "";
		var queryTypeCode = info.queryTypeCode || "";
		var userID = info.userID1 || info.loginUserID || "";
		var locID = info.locID || "";
		var errors = [];
		var seeOrderPromises = [];
		var caExecID = orderIDNeedToBeDispose.join("^")
		var chunks = vueComponent.utils.splitChunk(
		[],
		30,
		orderIDNeedToBeDispose
		);
		// debugger;

		orderHandleApi.checkOrderChunks = {
		    "url": "page/orderhandle/biz/checkOrderChunks",
		    "type": "post"
		}

		chunks.forEach(function(chunk) {
			seeOrderPromises.push(
			  ajax(
				orderHandleApi.checkOrderChunks,
				chunk.join("^"),
				userID,
				date,
				time,
				notes
			  ).then(function(result) {
				if (String(result.success) !== "0") {
				  errors = errors.concat(result.errList);
				}
			  })
			);
		});
		axios.all(seeOrderPromises).then(function() {
			//updateOrder();
			if (errors.length !== 0) {
				errors.forEach(function(error) {
				$message.error(error.errInfo);
				});
			} else {
				$message.success(vueComponent.$t("核实成功"));
			}
		});
  };
}
```

## 新增api

![[Pasted image 20231229164027.png]]
## 新增置数据方法

```
/// Creator:      songchao
/// CreateDate:   2018.6.7
/// Description:  按块核实医嘱
/// Input:       
/// Return:       
/// Other:        w ##Class(Nur.NIS.Service.Base.OrderHandle).CheckOrderChunks("1115374||1","7681","2019-12-25","10:04","123")
ClassMethod CheckOrderChunks(oeoriIdStr As %String, userId As %String, date As %String = "", time As %String = "", note As %String = "")
{
	n (oeoriIdStr,userId,type,note,date,time,logonLoc)
	s ^zhu("CheckOrderChunks")=$lb(oeoriIdStr,userId,type,note,date,time,logonLoc)
	

    // 初始化入参
	i (date="")&(time="") s date=$zd($h,3),time=$zt($p($h,",",2),2)
	s execDate=##class(websys.Conversions).DateHtmlToLogical(date)
	s execTime=##class(websys.Conversions).TimeHtmlToLogical(time)
	s createDate=$p($g(^OEORD(+oeoriIdStr,"I",+$p(oeoriIdStr,"||",2),3)),"^",7)         
    s createTime=$p($g(^OEORD(+oeoriIdStr,"I",+$p(oeoriIdStr,"||",2),1)),"^",17)

	// 初始化返回
    s retObj=##class(%ArrayOfDataTypes).%New()
    s errList=##class(%ListOfDataTypes).%New() 
    d retObj.SetAt(0,"success")
	d retObj.SetAt(errList,"errList")
	
	// 核对控制
    i (execDate<createDate)!((execDate=createDate)&(execTime<createTime)){
	    s errObject=##class(%ArrayOfDataTypes).%New()
	    d errObject.SetAt("核对医嘱时间应小于开时间","errInfo")
	    d errList.Insert(errObject)
	    d retObj.SetAt(1,"success")
	    q retObj
    }
    
	s checkOrderRet=..CheckOrder(oeoriIdStr,userId,execDate,execTime,note)
	i checkOrderRet'=0{
	    s errObject=##class(%ArrayOfDataTypes).%New()
	    d errObject.SetAt(checkOrderRet,"errInfo")
	    d errList.Insert(errObject)
	    d retObj.SetAt(1,"success")
	}
	
	q retObj
}

ClassMethod CheckOrder(OrderIdStr, UserId, Date, Time, Msg)
{
	s checkOrderRet = 0
	f i=1:1:$l(OrderIdStr,"^"){
		s orderId = $p(OrderIdStr,"^",i)
		q:checkOrderRet'=0
		s checkOneOrderRet = ..CheckOneOrder(orderId,UserId,Date,Time,Msg)
		//失败
		i checkOrderRet'=1{
			s CheckOrderRet = "核对失败:"_orderId
		}
	}
	q checkOrderRet
}

ClassMethod CheckOneOrder(OrderId, UserId, Date, Time, Msg)
{
	
	s obj = ##class(Nur.Z.Need.Bean.CheckOrderLog).%New()
	s obj.CheckOrderId = OrderId
	s obj.CheckUserId = UserId
	s obj.CheckDate = Date
	s obj.CheckTime = Time
	s obj.CheckMsg = Msg
	s checkOneOrderRet = obj.%Save()
	q checkOneOrderRet
}
```

## 新增类--增加实体，增加索引

```
Class Nur.Z.Need.Bean.CheckOrderLog Extends %Persistent
{

/// 医嘱id
Property CheckOrderId As %String [ SqlColumnNumber = 2, SqlFieldName = CheckOrderId ];

/// 核对人
Property CheckUserId As %String [ SqlColumnNumber = 3, SqlFieldName = CheckUserId ];

/// 核对日期
Property CheckDate As %Date [ SqlColumnNumber = 4, SqlFieldName = CheckDate ];

/// 核对时间
Property CheckTime As %Time [ SqlColumnNumber = 5, SqlFieldName = CheckTime ];

/// 核对附加信息
Property CheckMsg As %String [ SqlColumnNumber = 6, SqlFieldName = CheckMsg ];

Index CheckOrderId On CheckOrderId;

Index CheckUserId On CheckUserId;

Index CheckDateTime On (CheckDate, CheckTime);

Storage Default
{
<Data name="CheckOrderLogDefaultData">
<Value name="1">
<Value>%%CLASSNAME</Value>
</Value>
<Value name="2">
<Value>CheckOrderId</Value>
</Value>
<Value name="3">
<Value>CheckUserId</Value>
</Value>
<Value name="4">
<Value>CheckDate</Value>
</Value>
<Value name="5">
<Value>CheckTime</Value>
</Value>
<Value name="6">
<Value>CheckMsg</Value>
</Value>
</Data>
<DataLocation>^Nur.Z.Need.Bean.CheckOrderLogD</DataLocation>
<DefaultData>CheckOrderLogDefaultData</DefaultData>
<IdLocation>^Nur.Z.Need.Bean.CheckOrderLogD</IdLocation>
<IndexLocation>^Nur.Z.Need.Bean.CheckOrderLogI</IndexLocation>
<StreamLocation>^Nur.Z.Need.Bean.CheckOrderLogS</StreamLocation>
<Type>%Library.CacheStorage</Type>
}

}

```