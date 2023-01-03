
### 双向绑定

* vue2
    通过Object.defineProperty()的set和get
    在数组的实现时通过重写了数组的一些原型方法['push', 'pop', 'shift', 'unshift', 'splice', 'sort', 'reverse']

* vue3
    通过Proxy实现

Proxy的优势：
1. 丢掉麻烦的备份数据
2. 省去for in 循环
3. 可以监听数组变化
4. 代码更简化
5. 可以监听动态新增的特性
6. 可以监听删除的特性
7. 可以监听数组的索引和 Length 属性

```js
    let proxyObj = new Proxy(obj,{
        set:(target, prop, value)=>{
            target[prop] = value;
        }
        get:(target, prop)=>{ 
            return prop in target ? target[prop] : target
        }
    })
```

### 优化VDom

在vue2中每次更新diff都是全量对比; vue3则只对比带有标记的,大大减少了非动态内容的对比消耗

[Vue Template Explorer](https://template-explorer.vuejs.org/) ：看到静态标记

```js

TEXT = 1                           // 动态文本节点
CLASS=1<<1,                        // 2//动态class
STYLE=1<<2,                        // 4 //动态style
PROPS=1<<3,                        // 8 //动态属性，但不包含类名和样式
FULLPR0PS=1<<4,                    // 16 //具有动态key属性，当key改变时，需要进行完整的diff比较。
HYDRATE_ EVENTS = 1 << 5,          // 32 //带有监听事件的节点
STABLE FRAGMENT = 1 << 6,          // 64 //一个不会改变子节点顺序的fragment
KEYED_ FRAGMENT = 1 << 7,          // 128 //带有key属性的fragment 或部分子字节有key
UNKEYED FRAGMENT = 1<< 8,          // 256 //子节点没有key 的fragment
NEED PATCH = 1 << 9,               // 512 //一个节点只会进行非props比较
DYNAMIC_SLOTS = 1 << 10            // 1024 // 动态slot
HOISTED = -1                       // 静态节点
BALL = -2
```

我们发现创建动态 dom 元素的时候，Vdom 除了模拟出来了它的基本信息之外，还给它加了一个标记： 1 /* TEXT */

这个标记就叫做 patch flag（补丁标记）

patch flag 的强大之处在于，当你的 diff 算法走到 _createBlock 函数的时候，会忽略所有的静态节点，只对有标记的动态节点进行对比，而且在多层的嵌套下依然有效。

尽管 JavaScript 做 Vdom 的对比已经非常的快，但是 patch flag 的出现还是让 Vue3 的 Vdom 的性能得到了很大的提升，尤其是在针对大组件的时候。


### Fragment

vue3 允许我们支持`多个根节点`
```js
<template>
  <div>12</div>
  <div>23</div>
</template>
```

同时支持`render JSX` 写法
```js
render() {
    return (
        <div>
            {this.visable 
            ? ( <div>{this.obj.name}</div> ) 
            : ( <div>{this.obj.price}</div> )}
            <input v-model={this.val}></input>
            {[1, 2, 3].map((v) => { return <div>{v}-----</div>; })}
        </div>
    );
}
```

同时新增了`Suspense teleport`  和  `多 v-model` 用法 

### Tree shaking
简单来讲，就是在保持代码运行结果不变的前提下，去除无用的代码

在Vue2中，无论我们使用什么功能，它们最终都会出现在生产代码中。主要原因是Vue实例在项目中是单例的，捆绑程序无法检测到该对象的哪些属性在代码中被使用到

而Vue3源码引入tree shaking特性，将全局 API 进行分块。如果你不使用其某些功能，它们将不会包含在你的基础包中

就是比如你要用watch 就是import {watch} from 'vue' 其他的computed 没用到就不会给你打包减少体积


### Composition Api

Setup 语法糖式编程 

### readonly

返回一个只读的变量拷贝Proxy
* 当原变量改变时，readonly返回的值也会跟着改变
* 当原变量具有响应式时，readonly返回的值也会具有响应式，此时改变原变量，视图上的原变量和readonly返回值的渲染都会随之更新

```vue
<template>
    <input v-model="mime.name" />
    <input v-model="readMy.name" />
    <p>{{ readMy.name }}</p>
</template>
<script lang="ts" setup>
import { ref ,readonly } from 'vue'

const mime = ref({
    name: 'xiao yao',
    age: 18
})

const readMy = readonly(mime)
console.log(mime, readMy);

</script>
```

### toRaw

返回响应式变量的值，不影响原变量的响应式结构，只是提取响应式变量的['_rawValue']
* [注意]修改源变量的值或修改toRaw返回的变量，两者都会一起改变；不同的是`修改源变量的值视图会改变`而修改toRaw返回的变量不会影响视图

```vue
<template>
    <input v-model="mime.name" />
    <p>{{ mime }}</p>
</template>
<script lang="ts" setup>
import { ref, reactive, toRaw } from 'vue'

const mime = reactive({ name: 'xiao yao', age: 18 })
const you = ref({ name: 'xiao yang', age: 24 })

console.log(mime, toRaw(mime));
console.log(you.value, toRaw(you.value));
</script>
```