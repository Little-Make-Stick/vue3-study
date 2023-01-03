### 监听属性

语法 watch( target, callback, options )

1. 监听 引用类型的改变时，如果变量是 ref类型， 需要设置 { deep: true } ; reactive类型则不用设置
2. 监听 引用类型的改变，newV 和 oldV 的值相同，都为最新的
3. 监听 引用类型具体某个属性的变化，可以 **处理成函数引用** 或者 **处理成单个响应变量**，因为`watch第一个参数只接受 响应类型 | 数组 | 函数`
4. 如果监听的是 ref对象， 取属性时一样需要加上 `.value`
5. 监听多个值时可以以数组将监听的值组合起来，newV 和 oldV 此时也是数组
6. watch(...) 返回一个函数，执行这个函数可以结束 当前监听器

```vue
<template>
    <input type="text" v-model="message.foo.bar.name">
    <input type="number" v-model="message2.foo.bar.age">
    <p>{{ message }}</p>
    <p>{{ message2 }}</p>
    <button @click="() => watchId()">stop Msg2 Watch</button>
</template>
<script lang="ts" setup>
import { ref, reactive, watch, toRef } from 'vue';
const message = ref<any>({
    foo: {
        bar: {
            name: 'xiao yao',
            age: 18
        }
    }
})
const message2 = reactive<any>({
    foo: {
        bar: {
            name: 'xiao yang',
            age: 20
        }
    }
})
const watchId = watch(message2, (newV, oldV) => {
    console.log('message2 watch', newV, oldV);
}, {
    deep: true   // 深度监听
})

watch(() => message.value.foo.bar.name, (newV, oldV) => {
    console.log('message watch name (function)', newV, oldV);
})
watch(toRef(message2.foo.bar, 'age'), (newV, oldV) => {
    console.log('message2 watch age (toRef)', newV, oldV);
})
watch([message, message2], (newV, oldV) => {
    console.log('message, message2 watch', newV, oldV);
}, {
    deep: true
})
</script>
```