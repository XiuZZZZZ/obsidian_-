
```
/// 获取指定日期范围内婴儿的apgar评分
/// ##class(Nur.IP.Delivery).getApgarScore("747||1||3",4)
/// d ##class(%ResultSet).RunQuery("Nur.Z.Need.Delivery","QueryBabyApgarScore","2023-12-20","2023-12-26")
Query QueryBabyApgarScore(StartDate, EndDate) As websys.Query(ROWSPEC = "motherName:%String,babyName:%String,apgarOne:%String,apgarTwo:%String,apgarThree:%String,apgarFour:%String")
{
}

ClassMethod QueryBabyApgarScoreExecute(ByRef qHandle As %Binary, StartDate, EndDate) As %Status
{
	set repid=$I(^CacheTemp)
 	s ind=1
 	s qHandle=$lb(0,repid,0)
	//以下是实例代码
	s startDateL = ##Class(websys.Conversions).DateHtmlToLogical(StartDate)
	s endDateL = ##Class(websys.Conversions).DateHtmlToLogical(EndDate)
	f date=startDateL:1:endDateL{
		//^PAPRGi("BABY_BirthDate",birthDate,pregnacyId,"DEL",pregnacySid,"BABY",babyId)
		s pregnacyId = "0" f{
			s pregnacyId = $o(^PAPRGi("BABY_BirthDate",date,pregnacyId))
			q:pregnacyId=""
			s motherEpisodeId = $o(^PAADMi("Pregn",pregnacyId,""),-1)
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
						s motherEpisodeId = $o(^PAADMi("Pregn",pregnacyId,""),-1)
						s motherPersonId = $p(^PAADM(motherEpisodeId),"^",1)
						s motherName = $p(^PAPER(motherPersonId,"ALL"),"^",1)
						s babyName = $p(^PAPER(babyPersonId,"ALL"),"^",1)
						s babyAllId = pregnacyId_"||"_pregnacySid_"||"_babyId
						s apgarOne = ##class(Nur.IP.Delivery).getApgarScore(babyAllId,1).GetAt("total")
						s apgarTwo = ##class(Nur.IP.Delivery).getApgarScore(babyAllId,2).GetAt("total")
						s apgarThree = ##class(Nur.IP.Delivery).getApgarScore(babyAllId,3).GetAt("total")
						s apgarFour = ##class(Nur.IP.Delivery).getApgarScore(babyAllId,4).GetAt("total")
						set Data=$lb(motherName,babyName,apgarOne,apgarTwo,apgarThree,apgarFour)
						d OutQueryBabyApgarScore
					}
					
				}
			}
		}
	}
	Set qHandle=$lb(0,repid,0)
	Quit $$$OK
OutQueryBabyApgarScore
	
 	Set ^CacheTemp(repid,ind)=Data
 	Set ind=ind+1
	quit
}

```