



# 修改

## 新增方法

```
///Nur.NIS.Service.Base.Patient
ClassMethod GetMainNUrseImageInfo(EpisodeID)
{
	n (EpisodeID,%session,%request)
	s roomSub=$p($p(^PAADM(EpisodeID),"^",73),"||",2)
	q:roomSub="" ""
	s wardID=$p(^PAADM(EpisodeID),"^",70)
	s roomData=$g(^PAWARD(wardID,"BED",roomSub))
	s roomDR=$p(roomData,"^",3)
	s roomDesc = $p(^PAROOM(roomDR),"^",2)
	s desc=##class(Nur.NIS.Service.Base.Patient).GetMainNurse(EpisodeID)
	s image=##class(%ArrayOfDataTypes).%New()
	d image.SetAt("主管护士","category")
	d image.SetAt(desc,"title")
    d image.SetAt(desc ,"originTitle") 
	d image.SetAt("","src")
	d image.SetAt("","linkUrl")
	d image.SetAt(0,"sequence")
	q image
}
```

## 修改方法

操作：新增

```
//Nur.NIS.Service.Base.Patient
//GetBedIcons
		//插入主管护士信息  
		s bedRoomImageInfo=..GetMainNUrseImageInfo(EpisodeID)
		if (bedRoomImageInfo'=""){
			i bedRoomImageInfo.Count()'=0 d
			.d bedRoomImageInfo.SetAt("true","visible")
			.d images.Insert(bedRoomImageInfo)
		}
```