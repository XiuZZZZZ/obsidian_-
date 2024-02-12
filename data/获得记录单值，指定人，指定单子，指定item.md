```js
ClassMethod getValueByCodeJLD(EpisodeId, EmrCode, ItemStr, direction = "1")
{
	s id="",result=""
	s code=$ZConvert(EmrCode,"U")
	s par=$O(^Nur.DHCNurseRecParentI("Episode"," "_EpisodeId,""))
	i par="" q ""
	s getIdFlag=""
	if (EmrCode'="")&(EpisodeId'="")
	{
		s date = ""
		f{
			s date = $O(^Nur.DHCNurseRecSubI("TypDatTim"," "_code,date),direction)
			q:date=""||getIdFlag=1
			s time = ""
			f{
				s time = $O(^Nur.DHCNurseRecSubI("TypDatTim"," "_code,date,time),direction)
				q:time=""||getIdFlag=1
				s rw = ""
				f{
					s rw = $O(^Nur.DHCNurseRecSubI("TypDatTim"," "_code,date,time,par,rw),direction)
					q:rw=""||getIdFlag=1
					//判断是否作废
					s RecCancelDate = $list(^Nur.DHCNurseRecSubD(par,rw),65)
					continue:RecCancelDate'=""
					//找到数据id，退出循环，设置id用于下面具体的数据获取
					i rw'="" s id=par_"||"_rw
					i id'="" s getIdFlag = 1
				}

			}
		}
	  
	}
	if id'=""
	{
		s a=##class(Nur.DHCNurseRecSub).%OpenId(id)
		for i=1:1:$l(ItemStr,"^"){
			s item = $P(ItemStr,"^",i)
			s value = $ZOBJPROPERTY(a,item)
			s $p(result,"^",i)=value
		}
	}
	q result
}

```