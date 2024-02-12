
## main

- 点
	- 基础数据
		- ca账号密码
			- 2006      jml6
	- 更新
		- 更新文件：更新5个类
		- 更新护士执行
			- 更新NurseCa.js，引入NurseCa.js
			- 更新OrderExcute.js的执行方法
			- 测试
				- 查看表是否有数据  ---SELECT * FROM DHC_NurExecSign
		- 更新医嘱单
			- 更新c#的方法
			- 更新后台方法
			- 测试
				- 医嘱单开启ca的方法：##Class(web.DHCNurCASignVerify).GetIsVerifyCA(102)
				- 打开医嘱单测试
		- 更新护理病历
			- 开启护理病历ca
			- 测试
				- 调试入口：scripts/nurse/hisui-0.1.0/dist/js/Common.js  JobNumEnterToName（输入工号回车代入姓名）
