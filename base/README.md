### 基础语法

#### createApp()

传入 createApp 的对象实际上是一个组件，每个应用都需要一个“根组件”，其他组件将作为其子组件。

```js
import { createApp } from 'vue'
// 从一个单文件组件中导入根组件
import App from '@/views/App.vue'

const app = createApp(App)

app.mount('#app')
```

#### mount()

应用实例必须在调用了 .mount() 方法后才会渲染出来。


#### 配置捕获所有由子组件上抛而未被处理的错误


```js
app.config.errorHandler = (err) => {
    /* 处理错误 */
}
```

#### 样式多值

你可以对一个样式属性提供多个 (不同前缀的) 值

```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

#### .exact 修饰符

`.exact` 修饰符允许控制触发一个事件所需的确定组合的系统按键修饰符。

```html
<!-- 仅当按下 Ctrl 且未按任何其他键时才会触发 -->
<button @click.ctrl.exact="onCtrlClick">A</button>
```

#### true-value 和 false-value

true-value 和 false-value 是 Vue 特有的 attributes，仅支持和 v-model 配套使用。

```html
<input type="checkbox" v-model="toggle" :true-value="dynamicTrueValue" :false-value="dynamicFalseValue" />
```

#### 深度监听

deep 如果想侦听所有嵌套的变更，你需要深层侦听器

```js
watch: {
    attrs: {
        handler(newV) {
            console.log(newV)
        }, 
        deep: true,
    }
},
```

#### 立即执行

immediate 强制立即执行回调

```js
attrs: {
    handler(newV) {
        console.log(newV)
    }, 
    immediate: true
}
```

#### 更新后的DOM

`flush: 'post'` 在侦听器回调中能访问被 Vue 更新之后的DOM

```js
attrs: {
    handler(newV) {
        console.log(newV)
    }, 
    flush: 'post'
}
```


#### 设置/停止 监听器

要在组件卸载之前就停止一个侦听器，这时可以调用 `$watch() API` 返回的函数

```js
const unwatch = this.$watch('question', (newQuestion) => {
    // ...
})

unwatch()
```

#### 函数模板引用

`ref attribute` 还可以绑定为一个函数，会在每次组件更新时都被调用。

```html
<input :ref="(el) => { /* 将 el 赋值给一个数据属性或 ref 变量 */ }">
```

#### 限制对子组件实例的访问

expose 选项可以用于限制对子组件实例的访问

```js
export default {
    // 父组件通过模板引用访问到子组件实例后，仅能访问 publicData 和 publicMethod
    expose: ['publicData', 'publicMethod'],
    data() {
        return {
            publicData: 'foo',
            privateData: 'bar'
        }
    },
    methods: {
        publicMethod() {
            /* ... */
        },
        privateMethod() {
            /* ... */
        }
    }
}
```

#### is属性

某些 HTML 元素对于放在其中的元素类型有限制

```html
<!-- 以下写法将无法正确插入表格 -->
<table>
    <blog-post-row></blog-post-row>
</table>

<!-- 在原生html标签中，is的值必须加上"vue:"才能被解析为vue文件 -->
<table>
    <tr is="vue:blog-post-row"></tr>
</table>
```

#### 单向数据流

props: 子组件接收父组件的props是只读的，在子组件中props里面的值无法直接修改

```js
exports default {
    props: ['name'],
    created(){
        // 无意义，不可改
        // this.name = 'yyy'
    }
}
```

#### 多类型参数

一个 prop 可以被声明为允许多种类型

```js
exports default {
    props: {
        disabled: [Boolean, Number]
    },
}
```

#### 参数类型为自定义类

type 也可以是自定义的类或构造函数，Vue 将会通过 instanceof 来检查类型是否匹配。

```js
class Person {
    constructor(firstName, lastName) {
        this.firstName = firstName
        this.lastName = lastName
    }
}

export default {
    props: {
        author: Person
    }
}
```

#### 自定义组件配置 v-model

```html
<!-- CustomInput.vue -->
<template>
    <input :value="modelValue"
        @input="$emit('update:modelValue', $event.target.value)"
    />
</template>
<script>
export default {
    props: ['modelValue'],
    emits: ['update:modelValue']
}
</script>

<!-- 使用 -->
<CustomInput v-model="searchText" />
```