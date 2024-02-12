
$c(4)是病人 $c(3)是键值对

- 遍历成组医嘱执行记录
	- 病人数据  数组：paraDataArr  [[paraDataArr数据]]
	- 医嘱数据  数组：listDataArr  [[listDataArr数据]]
- 上面有几个数组，遍历几次
	- 获取病人数据  数组  paraData  [[paraData数据]]
	- 获取医嘱数据  数组  listData  [[listData数据]]
- listData中的每一条医嘱，获取section页
	- 把病人数据装到paraContent(数组)
	- 把医嘱数据状态paraContent(数组)
		- 计算每条医嘱应该的高度
		- 设置高度
- 获得医嘱的数据，对应病人id
- 前台整理医嘱的数据
- 前台再整理医嘱的数据

paraDataArr，存病人的键值对
listDataArr，存对应病人的医嘱串，用$c分割
