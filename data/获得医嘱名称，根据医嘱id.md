
```js
ClassMethod GetArcimDesc(){
	s arcimDesc = ""
	s arcimID = $p($g(^OEORD(orderRid,"I",orderSid,1)),"^",2)  
	i arcimID'="" s arcimDesc= $P($G(^ARCIM(+arcimID,$p(arcimID,"||",2),1)),"^",2)
	q arcimDesc
}

```