```sql
SELECT ITM_ObservStatus_DR,ITM_Code, ITM_Desc,ITM_RowId FROM MRC_ObservationItem WHERE ITM_HOSPITAL = "2" ORDER BY ITM_ObservStatus_DR

asc正序  
desc倒序

&sql(select PHRI_RowID into:PHRIRowID from DHC_InPhReqItm where PHRI_PHR_Parref=:phId )
```