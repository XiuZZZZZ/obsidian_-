
# 下载lodop
![[Pasted image 20230627000546.png]]
# 安装两个文件
![[Pasted image 20230627000559.png]]
# 打印table试例

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script language="javascript" src="./LodopFuncs.js"></script>
</head>
<style>

</style>

<body>
    <a href="javascript:myPreview()"><b>打印预览</b></a>
    <div id="my-table1">
        <style>
            table {
                border-collapse: collapse;
                /* 需要将边框合并在一起 */
            }

            td,
            th {
                border: 1px solid black;
                /* 定义每个单元格的边框 */
            }
        </style>
        <table>
            <tr>
                <td>1</td>
                <td>2</td>
                <td>3</td>
            </tr>
            <tr>
                <td>4</td>
                <td>5</td>
                <td>6</td>
            </tr>
            <tr>
                <td>7</td>
                <td>8</td>
                <td>9</td>
            </tr>
        </table>
    </div>
</body>
<script>
    function myPreview() {
        // 定义 Lodop 对象
        var LODOP = getLodop();


        // 添加第一个表格
        var tableHtml1 = document.getElementById("my-table1").innerHTML;
        LODOP.ADD_PRINT_TABLE(50, 50, 100, 200, tableHtml1);

        // 添加第二个表格
        var tableHtml2 = document.getElementById("my-table1").innerHTML;
        LODOP.ADD_PRINT_TABLE(50, 150, 300, 200, tableHtml2);

        // 翻页
        LODOP.NewPageA();
        // 添加第一个表格
        var tableHtml1 = document.getElementById("my-table1").innerHTML;
        LODOP.ADD_PRINT_TABLE(50, 50, 100, 200, tableHtml1);

        // 添加第二个表格
        var tableHtml2 = document.getElementById("my-table1").innerHTML;
        LODOP.ADD_PRINT_TABLE(50, 150, 300, 200, tableHtml2);

        // 预览打印
        LODOP.PRINT();
    }

</script>

</html>
```


