

# 过程

## 增加右上角切换


```
//D:\huli-zhuxiaohang\赫尔森-vue-20231122\src\page\bedChart\summaryGroup\index.vue
      <div :class="['settingArea', { lite: $attrs.HISUIStyleCode === 'lite' }]">
        <a-switch
          :checkedChildren="$t('在床')"
          :unCheckedChildren="$t('全部')"
          :checked="bedChart_isInBedMode"
          @change="$listeners.switchBedsView2()"
        />
        <a-switch
          :checkedChildren="$t('简要')"
          :unCheckedChildren="$t('详细')"
          :checked="bedChart_isDetailMode"
          @change="$listeners.switchBedsView()"
        />
        <adjust-size v-on="$listeners" v-bind="$attrs"></adjust-size>
      </div>
```

## 增加数据

```
//src\page\bedChart\index.vue
    <summary-group
      :bedChart_isInBedMode="bedChart_isInBedMode"
	>
```


## 元素切换方法实现

switchBedsView2
`src\page\bedChart\index.vue`


```
    switchBedsView2() {
      debugger
      this.bedChart_isInBedMode = !this.bedChart_isInBedMode;
      if (this.bedChart_isInBedMode==false){
        this.hiddenEmptyBed();
      }else{
        this.showEmptyBed();
      }
      localStorage.removeItem("bedChart_isInBedMode");
      localStorage.setItem(
        "bedChart_isInBedMode",
        JSON.stringify(this.bedChart_isInBedMode)
      );
    },
```


## 两个方法

`src\page\bedChart\index.vue`

```
    hiddenEmptyBed() {
      this.beds.forEach(item => {
        if(item.patient.episodeID==undefined){
          this.$set(item, "display", false);
        }
      });
    },
    showEmptyBed(){
      this.beds.forEach(item => {
        if(item.patient.episodeID==undefined){
          this.$set(item, "display", true);
        }
      });
    },
```

## 获取床位数据的时候判断是否隐藏床位

// fetch

```

	debugger
	if (this.bedChart_isDetailMode==true){
	  this.showEmptyBed();
	}else{
	  this.hiddenEmptyBed();
	}
```