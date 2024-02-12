




## 过程

### 调试，发现问题

发现打印body的时候会卡住

### 打印模板

![[Pasted image 20231026101907.png]]

### 删除一些列，发现问题列

执行人，绑定数据如下
Nur.NIS.Service.OrderExcute.OrderInfo:GetExecCtcpDesc

### 参考其它列，为什么这个列有问题

要求执行时间：Nur.NIS.Service.OrderExcute.OrderInfo:GetSttDateTime

### 修改js

scripts/nurse/nis/NursePrintComm/SheetPrint.js

![[Pasted image 20231026110844.png]]