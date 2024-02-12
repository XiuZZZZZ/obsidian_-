
## main

- 修改
	- [[#增加方法]]
	- 修改明细获取的诊断


## 增加方法

```
/// 获得前三个初步诊断，按照顺序  primaryDiagnois
/// ##class(Nur.Z.Need.Patient).GetprimaryDiags(1028533)
ClassMethod GetprimaryDiags(EpisodeId)
{
	s mrdiaId = $p(^PAADM(EpisodeId),"^",61)
	s num = 0
	s sid = "0" f{
		s sid = $o(^MR(mrdiaId,"DIA",sid))
		q:sid=""
		s mainFlag = $p(^MR(mrdiaId,"DIA",sid,1),"^",20)
		//诊断类型
		s typeDesc = ""
		s typeId = ^MR(mrdiaId,"DIA",sid,"TYP",1)
		i typeId'="" s typeDesc = $p(^MRC("DTYP",typeId),"^",2)
		continue:typeId'=3  	//初步诊断才继续
		s num = num + 1
		q:num>3
		s DiagnosNumber=$P($G(^MR(mrdiaId,"DIA",sid,"EPR")),"^",2) //取顺序号
		i DiagnosNumber="" s DiagnosNumber = "999"
		s desc =  ""
		s codeId = $p(^MR(mrdiaId,"DIA",sid),"^",1)
		i codeId'="" s desc = $p(^MRC("ID",codeId),"^",2)
		s sort(DiagnosNumber)=mrdiaId_"||"_sid_"^"_desc
	}
	s result = ""
	s index = "" f{
		s index = $o(sort(index))
		q:index=""
		s desc = $p(sort(index),"^",2)
		s result = $s(result="":desc,1:result_";"_desc)
		
	}
	q result
}
```