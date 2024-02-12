```js
ClassMethod getOrderByEpisNo(labNo)
{
	//^OEORD(0,"EpisNo",1000003895,52,42)
	s result=""
	s orderId=""
	f{
		s orderId=$o(^OEORD(0,"EpisNo",labNo,orderId))
		q:orderId=""
		s orderSub=""
		f{
			s orderSub=$o(^OEORD(0,"EpisNo",labNo,orderId,orderSub))
			q:orderSub=""
			s order = orderId_"||"_orderSub_"||1"
			i result="" s result=order
			e  s result=result_"^"_order
		}
	}
	q result
}

```