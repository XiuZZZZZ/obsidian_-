# 当前科室病区
```js
//cache--病区id--科室id
s wardId = $p($g(^PAADM(episodeId)),"^",70)
s bedId = $p($g(^PAADM(episodeId)),"^",73)
s wardDesc = $p(^PAWARD(wardId),"^",2)
s locId = $p($g(^PAADM(episodeId)),"^",4)
s locDesc = $p(^CTLOC(locId),"^",2)
```

```sql
//sql
SELECT PAADM_CurrentWard_DR,PAADM_DepCode_DR,* FROM PA_Adm WHERE PAADM_RowID = episodeId
```

# 当前医院id
```js
//cache--医院id
s locId = $p($g(^PAADM(episodeId)),"^",4)
s hospId = $p(^CTLOC(locId),"^",22)
s hospDesc = $p(^CT("HOSP",hospId),"^",2)

```

```sql
//sql
SELECT PAADM_DepCode_DR,* FROM PA_Adm WHERE PAADM_RowID = episodeId
SELECT CTLOC_Hospital_DR,* FROM CT_Loc WHERE CTLOC_RowID = PAADM_DepCode_DR
SELECT * FROM CT_Hospital WHERE HOSP_RowId = CTLOC_Hospital_DR

```