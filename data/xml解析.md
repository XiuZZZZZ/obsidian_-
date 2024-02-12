```
/// w ##class(Nur.Data.UnitMethod).xmlOut("","/DATAS/DATA/BACTERIA")
/// ##class(Nur.Data.Interface.Blood).xmlOut("<DATAS>", "/DATAS/DATA/BACTERIA")
ClassMethod xmlOutByStr(XmlData, Node)
{
	s placerNo = ""
	//s reStream = ##class(web.DHCENS.STBLL.YG.Method.GetJUNEFromYG).GetJUNE("<params><cmd>a</cmd><inpno></inpno></params>")
	//s reStream="<DATAS><DATA><INPNO>517097</INPNO><BACTERIA>金黄色葡萄球菌</BACTERIA><DLEVEL>多耐药</DLEVEL><FOCUS>MRSA</FOCUS></DATA><DATA><INPNO>453291</INPNO><BACTERIA>鲍曼不动杆菌</BACTERIA><DLEVEL>多耐药</DLEVEL><FOCUS>CRAB</FOCUS></DATA></DATAS>"
	s reStream=$zcvt(reStream,"I","UTF8")
	s sc = ##class(%XML.XPATH.Document).CreateFromString(reStream,.documentList)
	s sc = documentList.EvaluateExpression(node,"text()",.result)
	for i=1:1:result.Count(){
		s key = result.GetAt(i)
		s value = key.Value
		if (value'=""){
			s placerNo=value
		}
	}
	q placerNo
}


/// ##class(Nur.Data.Tools.UnitMethod).xmlOut(Stream,Node)
ClassMethod xmlOutByStrem(Stream As %GlobalCharacterStream, Node)
{
	s result = ""
	s sc = ##class(%XML.XPATH.Document).CreateFromStream(Stream,.documentList)
	s sc = documentList.EvaluateExpression(Node,"text()",.resultList)
	s num = resultList.Count()
	for i=1:1:num{
		s key = resultList.GetAt(i)
		s value = key.Value
		s result = $s(result'="":result_"^"_value,result="":value)
	}
	q result
}

```