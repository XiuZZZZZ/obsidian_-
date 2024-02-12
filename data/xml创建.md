```js
/// ##class(Nur.Data.UnitMethod).strToXml("Durat@^SpecColor@^age@37岁^arcimDesc@核酸混合检测（微信预约用） ^arcimshortDesc@核酸混合检测（微信预约用）")
ClassMethod strToXml(strData)
{
	i strData="" q "" 
	set writer=##class(%XML.Writer).%New()
	set writer.Indent=1
	set status=writer.OutputToString()
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}
	set status=writer.StartDocument()
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}
	set status=writer.RootElement("orders")
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}
	set status=writer.Element("order")
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}
	s len = $l(strData,"^")
	f i=1:1:len{
		s keyValue = $p(strData,"^",i)
		s kye = $p(keyValue,"@",1)
		s value = $p(keyValue,"@",2)
		d ##class(Nur.Data.UnitMethod).insertNode(.writer,kye,value)
	}
	set status=writer.EndElement()
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}
	set status=writer.EndRootElement()
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}
	set status=writer.EndDocument()
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}
	s result = writer.GetXMLString()
	q result
}
ClassMethod insertNode(writer, nodeName, nodeValue)
{
	set status=writer.Element(nodeName)
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}

	set status=writer.WriteChars(nodeValue)
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}

	set status=writer.EndElement()
	if $$$ISERR(status) {do $System.Status.DisplayError(status) quit}
}
```