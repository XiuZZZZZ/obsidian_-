NurMp.TemplateSet  GetCodeList

```
//是外部数据源
i dataResourceElement'="" {
	i $d(^dataSourceRef(TCindentityU,dataResourceElement)){
		s dataSourceRefOrigin = $o(^dataSourceRef(TCindentityU,dataResourceElement,""))
		i dataSourceRefOrigin'=dataSourceRef k ^dataSourceRef(TCindentityU,dataResourceElement)
	}
	
	s ^dataSourceRef(TCindentityU,dataResourceElement,dataSourceRef) = ""
}
```

![[Pasted image 20231015114211.png]]