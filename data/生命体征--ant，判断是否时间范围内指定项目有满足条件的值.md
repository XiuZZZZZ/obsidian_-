```
ClassMethod IfValid(EpisodeId, Type, StartDateL, StartTimeL, EndDateL, EndTimeL)
{
	s StartDateTimeL = StartDateL*100000+StartTimeL
	s EndDateTimeL = EndDateL*100000+EndTimeL
	s result = 3
	//遍历指定
	if Type=1{
		s itemId = 69
		//^MR(mrRid,"OBS",0,"Item",itemId,dateL,timeL,id)
		s mrId = $P(^PAADM(EpisodeId),"^",61)
		f dateL=StartDateL:1:EndDateL{
			q:result=1
			s timeL = "" f{
				q:result=1
				s timeL = $o(^MR(mrId,"OBS",0,"Item",itemId,dateL,timeL))
				q:timeL=""
				s rowId = $o(^MR(mrId,"OBS",0,"Item",itemId,dateL,timeL,""))
				s dateTimeL = dateL*100000+timeL
				continue:rowId=""
				continue:(dateTimeL<StartDateTimeL)||(dateTimeL>EndDateTimeL)
				s value = $p(^MR(mrId,"OBS",rowId),"^",2)
				if (value)&&(value>37.3){
					s result = 1
				}

			}
		}
	}
	q result
}
```