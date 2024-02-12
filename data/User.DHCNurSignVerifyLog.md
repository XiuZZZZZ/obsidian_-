```
Class User.DHCNurSignVerifyLog Extends (%Persistent, %XML.Adaptor) [ Inheritance = right, Owner = {_SYSTEM}, SqlRowIdName = SVL_Rowid, SqlTableName = DHC_NurSignVerifyLog ]
{

/// 医嘱ID或执行记录ID
Property SVLOEOREID As %String [ SqlColumnNumber = 2, SqlFieldName = SVL_OEOREID ];

/// 执行主要信息原文(使用HASH数据存储)
Property SVLOEOREMainInfo As %String(MAXLEN = 5000) [ SqlColumnNumber = 3, SqlFieldName = SVL_OEOREMainInfo ];

/// 执行主要信息签名证书(仅数据签名服务器验签需要此参数,其它P7和XML都封装在签名信息中)
Property SVLOEOREMainSignCert As %String(MAXLEN = 5000) [ SqlColumnNumber = 4, SqlFieldName = SVL_OEOREMainSignCert ];

/// 执行时间戳
Property SVLOEOREMainSignTimeStamp As %String(MAXLEN = 99999) [ SqlColumnNumber = 5, SqlFieldName = SVL_OEOREMainSignTimeStamp ];

/// 执行主要信息签名信息
Property SVLOEOREMainSignValue As %String(MAXLEN = 5000) [ SqlColumnNumber = 6, SqlFieldName = SVL_OEOREMainSignValue ];

/// 操作类型(1 执行医嘱 2 撤销执行 3 置皮试结果 4 打印执行单 5 打印瓶贴 6 打印条码)
Property SVLOperationType As %String [ SqlColumnNumber = 7, SqlFieldName = SVL_OperationType ];

/// 记录创建人
Property SVLCreateUserDR As %String [ SqlColumnNumber = 8, SqlFieldName = SVL_CreateUser_DR ];

/// 记录创建日期
Property SVLCreateDate As %Date [ SqlColumnNumber = 9, SqlFieldName = SVL_CreateDate ];

/// 记录创建时间
Property SVLCreateTime As %Time [ SqlColumnNumber = 10, SqlFieldName = SVL_CreateTime ];

/// 错误日志
Property SVLErrorMsg As %String(MAXLEN = 500) [ SqlColumnNumber = 11, SqlFieldName = SVL_ErrorMsg ];

Storage Default
{
<Data name="DHCNurSignVerifyLogDefaultData">
<Value name="1">
<Value>%%CLASSNAME</Value>
</Value>
<Value name="2">
<Value>SVLCreateDate</Value>
</Value>
<Value name="3">
<Value>SVLCreateTime</Value>
</Value>
<Value name="4">
<Value>SVLCreateUserDR</Value>
</Value>
<Value name="5">
<Value>SVLErrorMsg</Value>
</Value>
<Value name="6">
<Value>SVLOEOREID</Value>
</Value>
<Value name="7">
<Value>SVLOEOREMainInfo</Value>
</Value>
<Value name="8">
<Value>SVLOEOREMainSignCert</Value>
</Value>
<Value name="9">
<Value>SVLOEOREMainSignTimeStamp</Value>
</Value>
<Value name="10">
<Value>SVLOEOREMainSignValue</Value>
</Value>
<Value name="11">
<Value>SVLOperationType</Value>
</Value>
</Data>
<DataLocation>^User.DHCNurSignVerifyLogD</DataLocation>
<DefaultData>DHCNurSignVerifyLogDefaultData</DefaultData>
<IdLocation>^User.DHCNurSignVerifyLogD</IdLocation>
<IndexLocation>^User.DHCNurSignVerifyLogI</IndexLocation>
<StreamLocation>^User.DHCNurSignVerifyLogS</StreamLocation>
<Type>%Library.CacheStorage</Type>
}

}

```