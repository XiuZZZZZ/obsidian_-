


## 需求

- 排查过程
	- 报错 [[#报错]]
	- 找到按钮绑定方法
	- 按钮绑定方法对应value的加密
		- 在csp中加密的
	- 找到原因：删除对应的后台方法没有值
- 修改
	- 增加删除方法，删除置RecCancelDate，RecCancelTime，RecCancelUser三个值为空
	- 修改查询方法，如果这三个值不为空，则过滤不显示


callPreHyperEvent

## 报错
![[Pasted image 20231225092920.png]]