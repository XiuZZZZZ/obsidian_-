```
f EmrDate=StartDate:1:EndDate  d
.s EmrCode="" f  s EmrCode=$o(^NurMp.DHCTempMultDataI("emdate",EmrDate,EmrCode)) q:EmrCode=""  d
..q:(代表continue)
..// 以下是内容 xxxxxx
```