## 安装ant-desgin-vue包

运行命令

```
npm install -save ant-design-vue@1.5.0
```

不要使用全局安装

```
npm install -g ant-design-vue@1.5.0
```

## 在main.js中引用

①main.js中全局引入

```
// 引入AntD
import Antd from 'ant-design-vue';

// 引入AntD的样式文件
import 'ant-design-vue/dist/antd.css';

// 将AntD注册为Vue的插件
Vue.use(Antd)
```

main.js中局部引入

```
import { Button, message } from 'ant-design-vue';
Vue.use(Button，message)
```

## 测试一个ant的勾选框

```
<template>
	<a-checkbox @change="onChange">
		Checkbox
	</a-checkbox>
</template>


export default {
  methods: {
    onChange(e) {
      console.log(`checked = ${e.target.checked}`);
    },
  },
}
```