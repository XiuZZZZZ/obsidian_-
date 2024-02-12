```js
/// ##class(Nur.Data.Ca.TempTest).getGroupDesc(23)
ClassMethod get(episodeId, arcimId)
{

	//1.遍历医嘱
	s orderIdStr = ""
	s orderRid = $o(^OEORD(0,"Adm",episodeId,""))
	s date = ""
	f{
		s date = $o(^OEORDi("0","ARCIM",orderRid,arcimId,date))
		q:date=""
		s orderSid = ""
		f{
			s orderSid = $o(^OEORDi("0","ARCIM",orderRid,arcimId,date,orderSid))
			q:orderSid=""
			//2.遍历执行记录
			s orderEid = 0
			f{
				s orderEid = $o(^OEORD(orderRid,"I",orderSid,"X",orderEid))
				q:orderEid=""

				//3.过滤
				//过滤未执行
			    s excuteStatusId = $p(^OEORD(orderRid,"I",orderSid,"X",orderEid),"^",16)
			    i $g(excuteStatusId)'="" s excuteStatusDesc = $g(^OEC("STAT",$g(excuteStatusId)))
				continue:excuteStatusDesc'["F"

				//4.存数据，方便后面处理，自定
				s sort()

			}
		}
	}
}
```