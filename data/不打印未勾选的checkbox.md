
后台代码

类：Nur.DHCMoudData

方法：getVal

最后循环之前

```
/*
i (tmp("EmrCode")="DHCNURPGD_NKJMXSSSZFXPG") {
	f {
		s k=$O(tmp(k))
		q:k=""
		i k="Item48" b
		i tmp(k)["☑" {
			s tempValue = ""
			f i=1:1:$l(tmp(k),";"){
				s tempValueOne=""
				s charStr = ""
				i $p(tmp(k),";",i)["☑" s tempValueOne = $p(tmp(k),";",i)
				i tempValue="" {s tempValue = $g(tempValueOne)}
				else{
					s tempValue = tempValue_charStr_tempValueOne
				}
			}
			s tmp(k)=tempValue
		}
		i (tmp(k)["□")&&(tmp(k)'["☑") s tmp(k) = ""
	}
}*/
```