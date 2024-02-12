```js
/// ##class(Nur.Interface.OutSide.Order).getOrderIBydBarCode()
ClassMethod getOrderIBydBarCode(placerNo)
{
	s labNo=""
	s order=""
	f{
		s labNo=$o(^OEORDi(0,"PlacerNo",placerNo,labNo))
		q:labNo=""
		s orderId = ""
		f{
			s orderId = $o(^OEORDi(0,"PlacerNo",placerNo,labNo,orderId))
			q:orderId=""
			s orderSub = ""
			f{
				s orderSub = $o(^OEORDi(0,"PlacerNo",placerNo,labNo,orderId,orderSub))
				q:orderSub=""
				s order = orderId_"||"_orderSub_"||1"
			}
		}
	}
	q order
}

```