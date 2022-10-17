---
title: 混入mixins
date: 2021-07-13 21:22:28
tags: Vue
category: Vue
---

### [混入（mixins）](https://cn.vuejs.org/v2/guide/mixins.html)

-   官网指出： 混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。
-   当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。
-   同名钩子函数将合并为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子之前调用。

## 主要

> 定义看不懂

#### 定义混入组件

```
export default {
    data() {
        return {
            num : 0
        }
    },
    methods: {
        handleclick(){
            this.num = this.num ++
        }
    }
}


```

#### 混入引用到组件（局部）

总的说是混入到这里面就是和所有的重合到一起，这样可以获取到混入组件的相关内容了

```
   <template>
  <div>
      <h1>{{num}}</h1>
      <button @click="handleclick">点击每次加1</button>
  </div>
</template>

<script>
import minin from './minxin/minxin'
export default {
  mixins: [minin],
  data(){
    return {
    }
  },
  methods: {
  }
}
</script>
```

## 全局混入

> 定义全局混入,main.js 中引入。在需要的使用的页面直接使用，不需要再引入（使用方法同局部混入）

```
Vue.mixin({
    data () {
        rerutn {

        }
    },
    create () {},
    methods: {
        clgFn(){
            console.log('混入的mixin方法')
        },
    },
})

```

![BG图片](/img/1.jpg)
