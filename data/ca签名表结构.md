```sql
--开启ca的科室
--^DHCCERTAUTH.ConfigCTLoc
SELECT * FROM CA.ConfigCTLoc
	SELECT * FROM CT_Loc WHERE CTLOC_RowID IN ('171','34','188','35')

--医护人员类型 是否开启ca,有数据代表不开启ca
--^DHCCERTAUTH.ConfigCTCarPrvTp
SELECT * FROM CA.ConfigCTCarPrvTp

--日志表
--User.DHCNurSignVerifyLog
--DHC_NurSignVerifyLog

--业务表
--User.DHCNurExecSign
--DHC_NurExecSign
--保存方法：web.DHCNurSignVerify  InsertBatchSignRecord
SELECT * FROM DHC_NurExecSign

--用户是否开启ca
--CA.UsrSignatureInfo
--^DHCCERTAUTH.UsrSignatureInfo
SELECT UsrID, CertName,* FROM CA.UsrSignatureInfo
	SELECT * FROM SS_User WHERE SSUSR_RowId = '14178'

```