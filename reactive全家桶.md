### reactive

将引用类型的变量转化成响应式（深层次响应）

```vue
<template>
    <input v-model="mime.name" />
    <p>{{ list }}</p>
</template>
<script lang="ts" setup>
import { reactive } from 'vue'

const mime = reactive({
    name: 'xiao yao',
    age: 18
})

const list = reactive<string[]>([
    'John'
])
</script>
```

### isReactive

判断一个变量是否reactive

```vue
<template>
    <input v-model="mime.name" />
    <p>{{ mime.name }}</p>
</template>
<script lang="ts" setup>
import { reactive, isReactive } from 'vue'

const mime = reactive({
    name: 'xiao yao',
    age: 18,
})
console.log(isReactive(mime))
</script>
```

### ReactiveEffect

### guardReactiveProps

### shallowReactive

只能对浅层的数据具有响应式。 如果是深层的数据只会改变值,不会改变视图
响应式只到第一层，如下面的例子，name具有响应式，hobbies不具备响应式

```vue
<template>
    <input v-model="mime.name" />
    <p>{{ mime.hobbies }}</p>
</template>
<script lang="ts" setup>
import { shallowReactive } from 'vue'

const mime = shallowReactive({
    name: 'xiao yao',
    age: 18,
    hobbies: [
        '跑步',
        '跳绳'
    ]
})
setTimeout(() => {
    mime.hobbies.push('潜水')
    console.log(mime)
}, 2000)
</script>
```

### 拓展

* [拓展]ref 和 reactive的区别
    1. ref 适用于将 `所有类型`的变量转化为响应式, reactive 只适用于将 `引用类型`的变量转换为响应式
    2. ref 取/赋值都需要借助 `.value`, 而reactive 可以直接取值
    3. reactive 除了声明赋值后不能直接 赋值，否则会破坏 proxy 对象结构
