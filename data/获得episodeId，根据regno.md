```js
w ##class(Nur.Data.UnitMethod).getEpisodeIdByRegNo("0000000008","O","-1","1")
515
w ##class(Nur.Data.UnitMethod).getEpisodeIdByRegNo("0000000008","O","-1","2")
515^478
w ##class(Nur.Data.UnitMethod).getEpisodeIdByRegNo("0000000008","O","-1","")
515^478^476^347
4
/// ##class(Nur.Data.UnitMethod).getEpisodeIdByRegNo("0000000008","O","-1","1")
ClassMethod getEpisodeIdByRegNo(RegNo, Type, Direction = "1", Num)
{
	s episodeIdStr = ""
	s personId = $o(^PAPERi("PAPMI_PatNo",RegNo,""))
	q:personId="" ""
	s num = 0
	s episodeId = "" f{
		s episodeId = $o(^PAPERdr(personId,"ADM",Type,episodeId),Direction)
		q:episodeId=""
		q:(num=Num)&&(Num'="")
		s num = num + 1
		s episodeIdStr = $s(episodeIdStr="":episodeId,1:episodeIdStr_"^"_episodeId)
	}

	q episodeIdStr
}

```