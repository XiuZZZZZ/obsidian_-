

## 远程号
376 292 962
dn0fee2p
## 导入文件

### 导入cls

正常导入即可
### 导入csp

记得编译

### 导入js

js放到指定目录下

## 设置配置菜单

## 设置业务菜单

找到head菜单code："Nurse_BLXG"

打开菜单维护界面，仿照一个子菜单添加即可

## 调试内容

### 业务菜单打不开

是因为有历史数据，生成了当日的指向原来的交班配置的数据，需要清除

```
DELETE Nur_SHIFT.ShiftRecord WHERE SR_RECORD_DATE = "2023-11-10"
```
## 删除中间区

改class即可
```
/* 中间区 */
.midArea{
	display:none;
	padding:0 4px 10px 4px;	
	border-bottom:1px solid #ddd;
}
```

## 设置引入

配置即可，如果遇到问题一步一步排查

## 设置打印

### 设置外部数据源

### 绑定外部数据源

### 导入模板