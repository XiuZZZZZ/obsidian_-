

需要改一下js方法里面的计算

scripts/nurse/nis/HandleMethod/OrderExcute.js
lisBarPrintMerge

```
        oeordIdGroup = [];
        for (var k in obj) {
        	if (obj[k] !== "") {
        		oeordIdGroup.push(obj[k]);
        	}
        }

```

