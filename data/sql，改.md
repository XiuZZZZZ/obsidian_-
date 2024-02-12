```sql
//sqldb  
UPDATE OE_OrdExecExt SET OEORE_SkinTestAuditCTCP_DR = null WHERE OEORE_RowId = "1||7||1"  
//cache  
&sql(UPDATE SQLUser.OE_OrdExecExt SET OEORE_SkinTestAuditCTCP_DR=:provId WHERE OEORE_RowId=:orderId)
```