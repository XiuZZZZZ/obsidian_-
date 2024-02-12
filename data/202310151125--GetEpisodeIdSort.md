```
/// 获得时间范围内，在指定病区待过的就诊号
ClassMethod GetEpisodeIdSort(WardId, StartDate, EndDate)
{
	s startDateL = ##Class(websys.Conversions).DateHtmlToLogical(StartDate)
	s endDateL = ##Class(websys.Conversions).DateHtmlToLogical(EndDate)
	f dateL=startDateL:1:endDateL{
		s episodeId = "" f {
			s episodeId = $o(^PAADMi("AdminDateExt",dateL,episodeId))
			q:episodeId=""
			//判断这个病人是否有待这个病区
			i (WardId'="")&&$d(^PAADMi("TransWard",WardId,episodeId)){
				s ^||episodeIdSort(episodeId)=""
			}else{
				s ^||episodeIdSort(episodeId)=""
			}
		}
	}
}
```