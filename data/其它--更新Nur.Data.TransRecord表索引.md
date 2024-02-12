
- 远程号
	- 
- 打开表
	- Nur.Data.TransRecord
- 增加索引
	- Index TransWardID On (OrigWardID, TransDate, TransTime);  
	- Index TransLocID On (OrigLocID, TransDate, TransTime);
- 重建索引
	-  ##class(Nur.Data.TransRecord).%BuildIndices()