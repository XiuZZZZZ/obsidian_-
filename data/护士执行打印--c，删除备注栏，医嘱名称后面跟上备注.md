

- 新增数据源
	- 打开类，方法Nur.NurseExcuteSheet  getExcuteOrderInfo
	- 新增一个global s ^DHCCLNurseExec("Var",47)="医嘱名称带备注^arcimDescAndNote"
	- 指定位置增加代码行  `s val("arcimDescAndNote")=val("arcimDesc")_" "_val("notes")`
	
- 修改打印绑定的数据源
	- demo中打开菜单：护士打印设置
	- 选医院，住院
	- 左侧选中页签
	- 右侧删除备注列
	- 右侧修改医嘱名称绑定数据
	
- 修改打印程序
	- 定位打印content的时候
	- 横向扩展修改 = 改为 contains
	- 发布，获得改后的dll
	
	
- 中间件打印程序更新
	- 把上面的dll放到指定文件的zip中
	- 在demo的插件管理菜单中，把指定插件版本号+1