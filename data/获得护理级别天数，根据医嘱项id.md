```
/// zhuxiaohang
/// 【----获得执行记录的数据----】
/// 【output】一级护理天数^二级护理天数^三级护理天数^四级护理天数
/// 【input】病人就诊号,
/// ##class(Nur.Interface.OutSide.Order).getExcuteRecord(657)
ClassMethod getExcuteRecord(episodeId)
{
	s arcItmStr="4077||1^4079||1^4081||1^4075||1"
	s arcItmStr2="4082||1^4085||1^4081||1^4084||1"
	s orderId = $o(^OEORD(0,"Adm",episodeId,0)) 
	//遍历医嘱，获取有效护理级别医嘱id
	s result = "0^0^0^0"
	s orderDate = ""
    f
    {
    	s orderDate=$o(^OEORDi(0,"Date",orderId,orderDate))
    	q:orderDate=""
    	s orderTime = ""
    	f {
	    	s orderTime = $o(^OEORDi(0,"Date",orderId,orderDate,orderTime))
	    	q:orderTime=""
	    	s orderSub=0
	    	f {
	        		s orderSub=$o(^OEORDi(0,"Date",orderId,orderDate,orderTime,orderSub))
	        		q:orderSub=""
	        		s arcimId=$p($g(^OEORD(orderId,"I",orderSub,1)),"^",2) //医嘱项
	        		s orderEub=0
	        		f {
		        		s orderEub=$o(^OEORDi(0,"Date",orderId,orderDate,orderTime,orderSub,orderEub))
		        		q:orderEub=""
						s excuteStatusDesc = ""
						s excuteStatusId = $p(^OEORD(orderId,"I",orderSub,"X",orderEub),"^",16)
						i $g(excuteStatusId)'="" s excuteStatusDesc = $g(^OEC("STAT",$g(excuteStatusId)))
						q:excuteStatusDesc'["已执行"
		        		//判断是否是需要查询的医嘱
		        		i (arcItmStr[arcimId) {
			        		s arcimName = $p(^ARCIM(+arcimId,$p(arcimId,"||",2),1),"^",2)    //医嘱名称
			        		i (arcimId=$p(arcItmStr,"^",1))||(arcimId=$p(arcItmStr2,"^",1)) s $p(result,"^",1) = $p(result,"^",1) + 1
			        		i (arcimId=$p(arcItmStr,"^",2))||(arcimId=$p(arcItmStr2,"^",2)) s $p(result,"^",2) = $p(result,"^",2) + 1
			        		i (arcimId=$p(arcItmStr,"^",3))||(arcimId=$p(arcItmStr2,"^",3)) s $p(result,"^",3) = $p(result,"^",3) + 1
			        		i (arcimId=$p(arcItmStr,"^",4))||(arcimId=$p(arcItmStr2,"^",4)) s $p(result,"^",4) = $p(result,"^",4) + 1
			        	}
		        	}
		    }
	    }
    }
    q result
}
```