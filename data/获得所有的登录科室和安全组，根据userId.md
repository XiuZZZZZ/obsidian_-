```js
/// 获得所有的登录科室和安全组，根据userId
/// ##class(Nur.Data.UnitMethod).getAllLogonInfo(11)
ClassMethod getAllLogonInfo(UserId)
{
	s mainData = ^SSU("SSUSR",UserId)
	s sort(0)=0
        //主要登陆科室和安全组
	s locList = $p(mainData,"^",4)
	s groupList = $p(mainData,"^",5)
	s sort(0)=1
	s sort(1)=locList_"^"_groupList
	//其它登录科室
	s sub = 0
	f {
		s sub = $o(^SSU("SSUSR",UserId,"OTHLL",sub))
		q:sub=""
		s data = ^SSU("SSUSR",UserId,"OTHLL",sub)
		s loc=$p(data,"^",1)
		s group=$p(data,"^",2)
		s index = $o(sort(""),-1)+1
		s sort(index)=loc_"^"_group
		s sort(0) = sort(0)+1
	}
	q ""
}

```