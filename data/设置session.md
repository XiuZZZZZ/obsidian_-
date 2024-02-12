```js
i '$d(%session) {
	s %session = ##class(%CSP.Session).%New($j)
	s %session.Data("LOGON.WARDID") = 32
	s %session.Data("LOGON.CTLOCID") = 181
	s %session.Data("LOGON.USERID")
}
```