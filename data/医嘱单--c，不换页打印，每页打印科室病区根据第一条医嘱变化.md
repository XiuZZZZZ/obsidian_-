


## main

- 过程
	- 打印title的地方
		- `DHCCDoctorOrderSheet\Doctor\DoctorLocOrder.cs`
		- paint
			- paintTitle
	- 修改打印title的地方
		- 获取每一页第一行的数据 [[#获取每一页第一行数据]]

[链接文字](#修改OrderItem对象，增加科室和病区)



- 修改临时医嘱单
	- 增加医嘱数据对应关系
		- [[#修改OrderItem对象，增加科室和病区]]
		- 修改临时医嘱单的xml  [[#修改xml]]
		- 修改后台取值方法
			- 增加返回的值：AdapWARD，AdapLOC
			- [[#医嘱对应的科室和病区]]
	- c#，打印的时候获取第一条医嘱的数据
		- c#增加方法：获取指定页第一行的科室和病区  [[#getAdaptionTitle]]
		- c#修改方法：打印医嘱列数据  [[#drawOrder]]
		- c#重写方法：打印Title的时候  [[#drawTitle]]
	- 后台，医嘱数据对应的科室和病区
		- 


## 医嘱对应的科室和病区

在下面获取数据的前面

```
    ......i num#27=1 d
    .......//每页第一个
    .......s ward="测试数据"_num
    .......s loc="测试数据"_num
```

## drawTitle

类：DoctorLocOrder.cs

```
        public void drawTitle(Graphics g,int page)
        {
            for (int i = 0; i < xmlConfig["Title"].ChildNodes.Count; i++)
            {
                try
                {
                    XmlNode text = xmlConfig["Title"].ChildNodes[i];
                    this.drawText(g, text);
                    if (text.ChildNodes.Count != 0)
                    {
                        XmlNode dataNode = text.ChildNodes[0];
                        string dataCode = dataNode.Attributes["code"].Value;
                        if ((dataCode == "AdapWARD") || (dataCode == "AdapLOC"))
                        {
                            
                            string adaptionValue = this.printData.getAdaptionTitle(page, dataCode);
                            if (adaptionValue != "") {
                                dataNode.Attributes["value"].Value = adaptionValue;
                            }
                        }
                        else
                        {
                            dataNode.Attributes["value"].Value = this.patInfo[dataCode].ToString();
                        }

                        this.drawText(g, dataNode);
                    }
                }
                catch (NullReferenceException nullEx)
                {
                    Console.WriteLine(nullEx.Message);
                }

            }

        }
```

## drawOrder

类：DoctorPrintData

```
if (columnName.Contains("Adap")) {
	continue;
}
```

## getAdaptionTitle

类：DoctorPrintData

```
        public string getAdaptionTitle(int page,string columnName) {
            string value = "";
            if (this.printData.ContainsKey(page))
            {
                DoctorOrderPageData pageData = this.printData[page];
                if (pageData != null)
                {
                    DoctorOrderRowData rowData = pageData.pageData[1];
                    DoctorOrderCellData cellData = rowData.rowData[columnName];
                    value = cellData.Value;
              
                    
                }
            }
            return value;
        
        }
```

## 修改xml

修改title的WARD和LOC为AdapWARD和AdapLOC
```
<Text value="科别:" x="76" y="34" fontSize="10" fontFamily="宋体" viewColor="Blue" fontColor="Black" fontStyle="Regular">
	<data code="AdapLOC" value="" x="84" y="34" fontSize="10" fontFamily="宋体" viewColor="Blue" fontColor="Black" fontStyle="Bold"/></Text>
<Text value="病区:" x="111" y="34" fontSize="10" fontFamily="宋体" viewColor="Blue" fontColor="Black" fontStyle="Regular">
	<data code="AdapWARD" value="" x="120" y="34" fontSize="10" fontFamily="宋体" viewColor="Blue" fontColor="Black" fontStyle="Bold"/></Text>
```

增加row
```
<Text code="AdapWARD" titleFlag="true" width="0" height="8" fontSize="8" fontFamily="宋体" viewColor="Blue" lineColor="Black" fontColor="Black" fontStyle="Regular" Alignment="Near" LineAlignment="Center"></Text>
<Text code="AdapLOC" titleFlag="true" width="0" height="8" fontSize="8" fontFamily="宋体" viewColor="Blue" lineColor="Black" fontColor="Black" fontStyle="Regular" Alignment="Near" LineAlignment="Center"></Text>
```

## 修改OrderItem对象，增加科室和病区

```
public string AdapWARD { get; set; }//医嘱划线标志[Top,Bottom]

public string AdapLOC { get; set; }//医嘱划线标志[Top,Bottom]
```

## 获取每一页第一行数据

```
if (this.printData.ContainsKey(page))
{
	DoctorOrderPageData pageData = this.printData[page];
	if (pageData != null)
	{
		DoctorOrderRowData rowData = pageData.pageData[0];
		DoctorOrderCellData cellData = rowData.rowData[columnName];
		string value = cellData.Value;
	}
}
```