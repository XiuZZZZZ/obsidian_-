```
/// 获得就诊号，指定单子的填写记录
ClassMethod GetNurMpRowIdStr(Indentity)
{
	s episodeId = ""
	f{
		s episodeId = $o(^||episodeIdSort(episodeId))
		q:episodeId=""
		s rowIdStr = ""
		s IndentityU = $ZCONVERT(Indentity,"U")
		s TCIndentityId = $o(^NurMp.TemplateI("Indentity"," "_IndentityU,""))
		i TCIndentityId'=""{
			s TCIndentity = $list(^NurMp.TemplateD(TCIndentityId),4)
			s TCIndentityU = $ZCONVERT(TCIndentity,"U")
			s rowId="" f{
				s rowId = $o(^NurMp.DHCTempMultDataI("EmrCode"," "_TCIndentityU," "_episodeId,rowId),1)
				q:rowId=""
				//是否作废
				s cancelUser = $list(^NurMp.DHCTempMultDataD(rowId),75)
				continue:cancelUser'=""
				s rowIdStr = $s(rowIdStr="":rowId,1:rowIdStr_"^"_rowId)
			}
		}
		i rowIdStr'="" s ^||episodeIdSort(episodeId,"NurMpIdStr")=rowIdStr
		e  k ^||episodeIdSort(episodeId)
	}
}
```