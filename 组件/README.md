
## 组件

* 卡片组件
    ```vue
    <template>
        <div class='card-component'>
            <div class="card-component-header">
                <div class="title">{{ title }}</div>
                <div class="operation">
                    <slot name="operation"></slot>
                </div>
            </div>
            <div class="card-component-content">
                {{ content }}
            </div>
            <div class="card-component-footer" v-if="isFooter">
                <button class="default-btn">取消</button>
                <button class="primary-btn">确定</button>
            </div>
        </div>
    </template>
    <script lang='ts' setup>
    import {ref, reactive} from 'vue';
    const props = defineProps<{
        title: string,
        content: string,
        isFooter: boolean
    }>()
    </script>

    <style lang="less" scoped>
    // scoped: 样式隔离
    @borderColor: #ccc;
    .card-component{
        width: 100%;
        height: 100%;
        min-width: 800px;
        min-height: 500px;
        position: relative;
        border: 1px solid @borderColor;
        display: flex;
        flex-direction: column;
        &-header{
            height: 50px;
            border-bottom: 1px solid @borderColor;
            line-height: 100%;
            padding: 0 10px ;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        &-content{
            height: calc(100% - 100px);
            flex: 1;
            padding: 10px;
            text-align: left;
            line-height: 35px;
        }
        &-footer{
            height: 50px;
            border-top: 1px solid @borderColor;
            button {
                height: 40px;
                margin: 5px;
                padding: auto 15px;
                border-radius: 4px;
                &.default-btn{
                    background-color: #fff;
                    color: #000;
                }
                &.primary-btn{
                    background-color: rgb(20, 108, 231);
                    color: #fff;
                }
            }
        }
    }
    </style>
    ```

### 局部组件

在单个vue实例中注册的组件

```vue
<template>
    <CardVue v-bind="myCard">
        <template #operation>
            <button>+</button>
        </template>
    </CardVue>
</template>
<script lang="ts" setup >
import CardVue from './components/Card.vue';
type card = {
    title: string,
    content: string,
    isFooter: boolean
}
const myCard:card = {
    title: 'string',
    content: '摘要：使用 CSS transforms CSS transforms 通过一系列 CSS 属性实现，通过使用这些属性，可以对 HTML 元素进行线性仿射变形（affine linear transformations）。可以进行的变形包括旋转，倾斜，缩放以及位移，这些变形同时适用于平面与三维空间。 tr 阅读全文',
    isFooter: true
}
</script>
```

### 全局组件

在全局Vue实例上挂载的组件, 

```js
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)

import CardVue from './components/Card.vue'
app.component('CardVue', CardVue)
// 使用时直接 <CardVue></CardVue> 即可，无需在页面引入

app.mount('#app')
```

### 批量导入全局组件

批量引入组件并配置成 对象{组件名： 组件}
```js
// vue3可以用 import.meta.globEager 引入多个文件
const files = import.meta.globEager("@/components/*.vue")
// console.log(files);

let components = {}

for( let [key, val] of Object.entries(files)) {
    let file = val.default
    // 根据文件名称和文件 组建键值对
    components[file.__name] = file
}

export default components
```

```js
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)

import components_ from '@/plugins/components.js'
console.log(components_);

for(let [key, comp] of Object.entries(components_)){
    app.component(key, <any>comp)
}

app.mount('#app')
```

### 动态组件

1. 由于reactive只能使用于引用类型，如果这里想要使用reactive,需要以键值对的格式赋给componentId,例如reactive({Comp: CompA})
2. 将组件转为ref对象时，(为了节省性能开销)不需要深层次响应，如果直接使用ref此时会报警告
    ```js
    /**
      * [Vue warn]: Vue received a Component which was made a reactive object. 
      * This can lead to unnecessary performance overhead, and should be avoided 
      * by marking the component with `markRaw` or using `shallowRef` instead of `ref`. 
      */
    ```
    根据警告可以得知`使用markRaw或者shallowRef可以达到浅层次的响应`消除警告。或者`给is传的是组件名而不是组件对象`
3. 不管是vue2还是vue3，都可以通过给`<component>`的is属性传 `组件名` 或者 `组件对象`,如果传的是组件对象，记得浅层性监听以免浪费性能

* CompA.vue
  ```vue 
  <template><p>Comp A</p></template>
  ```
* CompB.vue
  ```vue
  <template><p>Comp B</p></template>
  ```
* App.vue
  ```vue
  <template>
      <button @click="switchComp('A')">CompA</button>
      <button @click="switchComp('B')">CompB</button>
      <!-- ref -->
      <component :is="componentId"></component>
      <!-- reactive -->
      <!-- <component :is="componentId.Comp"></component> -->
  </template>
  <script lang="ts" setup >
  import { ref, shallowRef, reactive, shallowReactive, markRaw } from 'vue';
  import CompA from './components/CompA.vue'
  import CompB from './components/CompB.vue'
  /**
   * [Vue warn]: Vue received a Component which was made a reactive object. 
   * This can lead to unnecessary performance overhead, and should be avoided 
   * by marking the component with `markRaw` or using `shallowRef` instead of `ref`. 
   */
  
  let componentId: any = ref(markRaw(CompA))
  // let componentId: any = shallowRef(CompA)
  // let componentId: any = reactive({Comp: markRaw(CompA)})
  // let componentId: any = shallowReactive({Comp: CompA})
  const switchComp = (comp: string) => {
      componentId.value = comp == 'A' ? markRaw(CompA) : markRaw(CompB)
      // componentId.value = comp == 'A' ? CompA : CompB
      // componentId.Comp = comp == 'A' ? markRaw(CompA) : markRaw(CompB)
      // componentId.Comp = comp == 'A' ? CompA : CompB
  }
  </script>
  
  <style scoped>
  </style>
  ```

### 递归组件

组件内部调用自身循环生成模块。
vue3组合式API可以直接通过组件名使用自身，vue2及vue3选项式API 需要另外配置(例如 name: 'Tree')

```vue
<template>
    <div class='tree' >
        <!-- .stop阻止子级的click事件向上冒泡 -->
        <div class="tree-item" v-for="item in tree" :key="item.label" @click.stop="clickItem(item)">
            <input type="checkbox" v-model="item.checked">{{ item.label }}
            <!-- Vue3 组合式API可以直接根据文件名使用该组件 -->
            <!-- <Tree v-if="item.children?.length" :tree="item.children"></Tree> -->
            <!-- vue2 或者 vue3选项式API 可以通过定义 name 注册自身使用该组件 -->
            <Tree1 v-if="item.children?.length" :tree="item.children"></Tree1>
        </div>
    </div>
</template>
<script lang='ts' setup>
import {ref, reactive} from 'vue';
type treeObj = {
    label: string,
    checked: boolean,
    children?: treeObj[] | []
}

type Props<T> = {
  tree?: T[] | [];
};

const props = defineProps<Props<treeObj>>()
const clickItem = (item: treeObj) => {
    console.log('click', item.label);
}

</script>
<script lang="ts">
export default{
    // vue3组合式API 如果需要自定义组件名可以通过添加 选项式API写法
    name: 'Tree1'
}
</script>
<style scoped lang="less">
.tree { padding-left: 4em; }
</style>
```

调用递归组件
```vue
<template>
    <Tree1 :tree="tree"></Tree1>
</template>
<script lang="ts" setup >
import { reactive } from 'vue';
type treeObj = {
    label: string,
    checked: boolean,
    // ?: 不一定存在此属性
    children?: treeObj[] | []
}
const tree = reactive<treeObj[]>([
    {
        label: '选项1',
        checked: false,
        children: [
            {
                label: '选项1-1',
                checked: false,
            }
        ],
    },
    {
        label: '选项2',
        checked: false,
    },
    {
        label: '选项3',
        checked: true,
        children: [
            {
                label: '选项3-1',
                checked: false,
                children: [
                    {
                        label: '选项3-1-1',
                        checked: false,
                    }
                ],
            },
            {
                label: '选项3-2',
                checked: false,
            },
        ],
    },
])
</script>
<style scoped></style>
```
