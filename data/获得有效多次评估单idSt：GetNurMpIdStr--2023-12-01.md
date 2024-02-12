
```
/// ##class(Nur.Custom.NeedUnit.NurMpToShift).GetNurMpIdStr(50370,"DHCNURZYHZNRSYYFXPGJGYCSJLD")
ClassMethod GetNurMpIdStr(EpisodeId, Indentity)
{
	s rowIdStr = ""
	s IndentityU = $ZCONVERT(Indentity,"U")
	s TCIndentityId = $o(^NurMp.TemplateI("Indentity"," "_IndentityU,""))
	i TCIndentityId'=""{
		s TCIndentity = $list(^NurMp.TemplateD(TCIndentityId),4)
		s TCIndentityU = $ZCONVERT(TCIndentity,"U")
		s rowId="" f{
			s rowId = $o(^NurMp.DHCTempMultDataI("EmrCode"," "_TCIndentityU," "_EpisodeId,rowId))
			q:rowId=""
			//是否作废
			s cancelUser = $list(^NurMp.DHCTempMultDataD(rowId),75)
			continue:cancelUser'=""
			s rowIdStr = $s(rowIdStr="":rowId,1:rowIdStr_"^"_rowId)
		}
	}
	q rowIdStr
}
```