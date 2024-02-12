
hxey.blspecprint.csp


## 需求

- 导入lodop  [[#导入lodop]]
- 设置xml模板  [[#xml模板]]
- 实现模板的读取，数据的打印  [[#打印]]



## 打印

```
DHCP_GetXMLConfig("InvPrintEncrypt",CUR_PrintTemp);
var LODOP = getLodop();
var MyPara="SeqNo"+PDlime+SeqNo
	+"^"+"PatName"+PDlime+PatName
	+"^"+"Sex"+PDlime+Sex+"^"
	+"Age"+PDlime+Age  
DHC_PrintByLodop(LODOP, MyPara, MyList, [], "贴瓶签打印");
```


## 导入lodop

```
<script type="text/javascript" src="../scripts_lib/lodop/LodopFuncs.js"></script>
```

## xml模板

xml模板名：HXEYBLSpecPrint
模板描述：病理标签打印
模板示意图：
![[Pasted image 20231225123304.png]]


```
<?xml version="1.0" encoding="gb2312" ?>
<appsetting>
<invoice LandscapeOrientation="X" height="7.01" width="5" PrtPaperSet="WIN" PrtDevice="shuyeka" PrtPage="" PaperDesc="" XMLClassMethod="" XMLClassQuery="">
<TxtData RePrtHeadFlag="N">
<txtdatapara name="SeqNo" xcol="3.175" yrow="21.164" defaultvalue="" printvalue="" fontbold="true" fontname="宋体" fontsize="10" />
<txtdatapara name="PatName" xcol="21.429" yrow="20.37" defaultvalue="" printvalue="" fontbold="true" fontname="宋体" fontsize="12" />
<txtdatapara name="Sex" xcol="3.968" yrow="28.836" defaultvalue="" printvalue="" fontbold="false" fontname="宋体" fontsize="10" />
<txtdatapara name="Age" xcol="21.164" yrow="28.836" defaultvalue="" printvalue="" fontbold="false" fontname="宋体" fontsize="10" />
<txtdatapara name="SpecType" xcol="19.577" yrow="35.45" defaultvalue="" printvalue="" fontbold="false" fontname="宋体" fontsize="10" />
<txtdatapara name="LocDesc" xcol="3.968" yrow="43.386" defaultvalue="" printvalue="" fontbold="false" fontname="宋体" fontsize="10" />
<txtdatapara name="PrintDate" xcol="3.968" yrow="57.672" defaultvalue="" printvalue="" fontbold="false" fontname="宋体" fontsize="10" />
<txtdatapara name="PrintTime" xcol="24.074" yrow="57.672" defaultvalue="" printvalue="" fontbold="false" fontname="宋体" fontsize="10" />
<txtdatapara name="PrintUser" xcol="16.667" yrow="49.735" defaultvalue="" printvalue="" fontbold="false" fontname="宋体" fontsize="10" />
<txtdatapara name="Label10" xcol="3.968" yrow="35.45" defaultvalue="标本类型:" printvalue="" fontbold="false" fontname="宋体" fontsize="10" />
<txtdatapara name="label11" xcol="3.968" yrow="49.735" defaultvalue="打印人:" printvalue="" fontbold="false" fontname="宋体" fontsize="10" />
<txtdatapara name="specNo" xcol="2.381" yrow="1.587" defaultvalue="" printvalue="" fontbold="false" fontname="C39P36DmTt" fontsize="38" />
<txtdatapara name="specNo2" xcol="2.381" yrow="15.344" defaultvalue="" printvalue="" fontbold="false" fontname="宋体" fontsize="9" />
</TxtData>
<ListData PrintType="List" YStep="2.91" XStep="0" CurrentRow="1" PageRows="50" RePrtHeadFlag="Y" BackSlashWidth="0"></ListData>
<PICData RePrtHeadFlag="N"></PICData>
<PLData RePrtHeadFlag="Y"></PLData>
</invoice>
</appsetting>

```