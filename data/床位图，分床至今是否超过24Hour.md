

```

/// 需求：是否距离首次分床时间已经过24小时。
/// 入参：就诊号
/// 返回值：是则返回1，否则返回0。
/// ##class(Nur.Z.Need.Patient).GetPass24HourForFirstToBed()
ClassMethod GetPass24HourForFirstToBed(EpisodeId)
{
	s pass24Hour = 0
	s passTimeL = 0
	s transBed = ""
	s transBedDateL=""
	s transBedTimeL=""
	s transId = 0 f{
		s transId = $o(^PAADM(EpisodeId,"TRANS",transId))
		q:transId=""
		q:transBed'=""
		s transBed = $p(^PAADM(EpisodeId,"TRANS",transId),"^",8)
		continue:transBed=""
		s transBedDateL = $p(^PAADM(EpisodeId,"TRANS",transId),"^",1)
		s transBedTimeL = $p(^PAADM(EpisodeId,"TRANS",transId),"^",2)
	}
	i (transBedDateL'="")&&(transBedTimeL'=""){
		s curDateL = $p($h,",",1)
		s currTimeL = $p($h,",",2)
		i curDateL=transBedDateL{
			s passTimeL = currTimeL - transBedTimeL
		}else{
			s passTimeL1 = (86400 - transBedTimeL + currTimeL)
			s passTimeL2 = (curDateL-transBedDateL-1)*86400
			s passTimeL = passTimeL1+passTimeL2
		}
	}
	i passTimeL>86400{
		s pass24Hour = 1
	}
	q pass24Hour
}

```