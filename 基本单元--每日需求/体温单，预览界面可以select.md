---
日期: 2023-07-26T00:00:00.000+08:00
项目: 铜梁
newColumn7: 曾浩林
完成: true
更新: true
需求号: "3739228"
测试: true
业务: 体温单--svg
---
# 修改

## 增加方法

[web/scripts/nurse/hisui/singleEntry.js](https://1.1.6.18:1443/imedical/web/scripts/nurse/hisui/singleEntry.js)

```text
function setRealValue(code,value){
    if(code=='defFreq'){
        debugger
    var newValue = value; // 要插入的新值

    // 获取当前ComboBox的数据
    var data = $('#'+code).combobox('getData');

    // 判断新值是否已存在
    var exists = false;
    for (var i = 0; i < data.length; i++) {
      if (data[i].value === newValue) {
        exists = true;
        break;
      }
    }
    debugger
    if (!exists) {
      // 将新值插入到数据列表的最后
      data.push({value: newValue, text: newValue, selected:true, disabled:false});

      // 使用setData方法更新ComboBox的数据
      $('#'+code).combobox('loadData', data);

      // 设置ComboBox的值为新插入的值
      $('#'+code).combobox('setValue', newValue);

      console.log("新值已插入：", newValue);
    } else {
      console.log("该值已存在：", newValue);
    }
    }
}
```

## 调用位置

[web/scripts/nurse/hisui/singleEntry.js](https://1.1.6.18:1443/imedical/web/scripts/nurse/hisui/singleEntry.js)

findTempDataByDay

![](https://secure2.wostatic.cn/static/8RJo6GKKRC5kqx1hSbqGk6/image.png?auth_key=1690536054-uoJ3BebRgiBQRPFhwFL1wZ-0-f8e71e672e8a8d5a08f47893f4fe728a)

# 其它

## github还是有点问题

查询是不准的