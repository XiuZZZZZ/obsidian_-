

```
/// ##class(Nur.Z.Need.Delivery).GetDeliveryNum("2023-12-20","2023-12-26","Mother")
ClassMethod GetDeliveryNum(StartDate, EndDate, Type)
{
	s motherNum = 0
	s babyNum = 0
	s num = 0
	s startDateL = ##Class(websys.Conversions).DateHtmlToLogical(StartDate)
	s endDateL = ##Class(websys.Conversions).DateHtmlToLogical(EndDate)
	f date=startDateL:1:endDateL{
		//^PAPRGi("BABY_BirthDate",birthDate,pregnacyId,"DEL",pregnacySid,"BABY",babyId)
		s pregnacyId = "0" f{
			s pregnacyId = $o(^PAPRGi("BABY_BirthDate",date,pregnacyId))
			q:pregnacyId=""
			s motherEpisodeId = $o(^PAADMi("Pregn",pregnacyId,""),-1)
			s validBaby = "" 
			s pregnacySid = "0" f{
				s pregnacySid = $o(^PAPRGi("BABY_BirthDate",date,pregnacyId,"DEL",pregnacySid))
				q:pregnacySid=""
				s babyId = "0" f{
					s babyId = $o(^PAPRGi("BABY_BirthDate",date,pregnacyId,"DEL",pregnacySid,"BABY",babyId))
					q:babyId=""
					s babyPersonId = $p(^PAPRG(pregnacyId,"DEL",pregnacySid,"BABY",babyId),"^",73)
					s sort("motherEpisodeId",motherEpisodeId)=""
					s sort("motherEpisodeId",motherEpisodeId,"babyPersonId",babyPersonId)=""
					//如果婴儿是活产
					s babyOutId = $p(^PAPRG(pregnacyId,"DEL",pregnacySid,"BABY",babyId),"^",23)
					if (babyOutId=3){
						s validBaby = 1
						s babyNum = babyNum + 1
					}
					
				}
			}
			i validBaby=1{
				s motherNum = motherNum + 1
			}
		}
	}
	if (Type="Mother"){
		s num = motherNum
	}
	if (Type="Baby"){
		s num = babyNum
	}
	q num
}
```