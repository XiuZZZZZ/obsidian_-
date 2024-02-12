```js
/// ##class(Nur.Custom.Hospital.UnitMethod).getUserIdByInitials("nurse")
ClassMethod getUserIdByInitials(Initials)
{
	//^SSU("SSUSR",0,"SSUSR_Initials",$$ALPHAUP(Initials),id)
	q:Initials="" ""
	s InitialsU = $ZCONVERT(Initials,"U")
	s userId = $o(^SSU("SSUSR",0,"SSUSR_Initials",InitialsU,""))
	q userId
}

```