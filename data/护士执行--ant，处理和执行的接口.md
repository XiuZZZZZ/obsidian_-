```
【处理】
##Class(Nur.CommonInterface.OrderHandle).SeeOrderChunks(oeoriIdStr,userId,type,note,date,time,logonLoc)
参数说明
参数oeoriIdStr：医嘱串
参数userId：用户id
参数type：A是接收，C是拒绝
参数note：备注
参数date：日期
参数time：时间
参数LogonLoc：病区的科室id
返回值：
  json.success == 0代表成功
  json.success == 1代表失败，json.errList 是错误信息，是一个Array对象

具体：("697069||68^697069||65","14585","A","","2023-10-25","09:46",91)

【撤销处理】
##Class(Nur.CommonInterface.OrderHandle).SeeOrderChunks(oeoriIdStr,userId,logonLoc)
参数说明
参数oeoriIdStr：医嘱串
参数userId：用户id
参数logonLoc：病区的科室id
返回值：
    json.success == 0代表成功
  json.success == 1代表失败，json.errList 是错误信息，是一个Array对象
```