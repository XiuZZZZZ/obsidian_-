```
/// CA认证(数字证书认证)
Class User.DHCNurExecSign Extends (%Persistent, %XML.Adaptor) [ Inheritance = right, Owner = {_SYSTEM}, SqlRowIdName = SV_Rowid, SqlTableName = DHC_NurExecSign ]
{

/// 医嘱ID或执行记录ID
Property SVOEOREID As %String [ SqlColumnNumber = 2, SqlFieldName = SV_OEOREID ];

/// 执行主要信息原文(使用HASH数据存储)
Property SVOEOREMainInfo As %String(MAXLEN = 5000) [ SqlColumnNumber = 3, SqlFieldName = SV_OEOREMainInfo ];

/// 执行主要信息签名证书(仅数据签名服务器验签需要此参数,其它P7和XML都封装在签名信息中)
Property SVOEOREMainSignCert As %String(MAXLEN = 5000) [ SqlColumnNumber = 4, SqlFieldName = SV_OEOREMainSignCert ];

/// 执行时间戳
Property SVOEOREMainSignTimeStamp As %String(MAXLEN = 99999) [ SqlColumnNumber = 5, SqlFieldName = SV_OEOREMainSignTimeStamp ];

/// 执行主要信息签名信息
Property SVOEOREMainSignValue As %String(MAXLEN = 5000) [ SqlColumnNumber = 6, SqlFieldName = SV_OEOREMainSignValue ];

/// 操作类型(1 执行医嘱 2 撤销执行 3 置皮试结果 4 打印执行单 5 打印瓶贴 6 打印条码 7 处理医嘱)
Property SVOperationType As %String [ SqlColumnNumber = 7, SqlFieldName = SV_OperationType ];

/// 记录创建人
Property SVCreateUserDR As %String [ SqlColumnNumber = 8, SqlFieldName = SV_CreateUser_DR ];

/// 记录创建日期
Property SVCreateDate As %String [ SqlColumnNumber = 9, SqlFieldName = SV_CreateDate ];

/// 记录创建时间
Property SVCreateTime As %String [ SqlColumnNumber = 10, SqlFieldName = SV_CreateTime ];

/// 指向 CA.DigitalSignature (电子病历接口表关联)
Property SVDigitalSignatureDR As CA.DigitalSignature [ SqlColumnNumber = 11, SqlFieldName = SV_DigitalSignature_DR ];

Index OEOREID On SVOEOREID;

Storage Default
{
<Data name="DHCNurExecSignDefaultData">
<Value name="1">
<Value>%%CLASSNAME</Value>
</Value>
<Value name="2">
<Value>SVCreateDate</Value>
</Value>
<Value name="3">
<Value>SVCreateTime</Value>
</Value>
<Value name="4">
<Value>SVCreateUserDR</Value>
</Value>
<Value name="5">
<Value>SVOEOREID</Value>
</Value>
<Value name="6">
<Value>SVOEOREMainInfo</Value>
</Value>
<Value name="7">
<Value>SVOEOREMainSignCert</Value>
</Value>
<Value name="8">
<Value>SVOEOREMainSignTimeStamp</Value>
</Value>
<Value name="9">
<Value>SVOEOREMainSignValue</Value>
</Value>
<Value name="10">
<Value>SVOperationType</Value>
</Value>
<Value name="11">
<Value>SVDigitalSignatureDR</Value>
</Value>
</Data>
<DataLocation>^User.DHCNurExecSignD</DataLocation>
<DefaultData>DHCNurExecSignDefaultData</DefaultData>
<IdLocation>^User.DHCNurExecSignD</IdLocation>
<IndexLocation>^User.DHCNurExecSignI</IndexLocation>
<StreamLocation>^User.DHCNurExecSignS</StreamLocation>
<Type>%Library.CacheStorage</Type>
}

}

```