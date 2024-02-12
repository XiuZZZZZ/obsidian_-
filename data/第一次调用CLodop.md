

## 新建一个文件夹


## 文件夹下放lodop提供的js


## 新建一个html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script language="javascript" src="./LodopFuncs.js"></script>
</head>
<body>
    <button onclick="aaa()">aaa</button>
</body>

<script language="javascript" type="text/javascript"> 
    function aaa(){
        var LODOP=getCLodop();
        //有了该对象之后，就可以调用云打印功能语句了，如
        LODOP.PRINT();   
            LODOP.PREVIEW();   
        LODOP.PRINT_DESIGN();
    }
</script>

</html>
```

## 点击按钮