```
SELECT count(1) FROM Nur.DHCNurseRecSub a,PA_Adm b
WHERE a.CareDate BETWEEN '2023-04-01' AND '2023-06-30'
AND a.Adm=b.PAADM_RowID
AND b.PAADM_Type='I'
```