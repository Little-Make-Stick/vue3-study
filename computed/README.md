### 计算属性

语法 computed(getter | { setter, getter })

返回一个变量的简单变形，两种写法：一种直接设置set和get；一种直接设置get
```vue
<template>
    <input type="text" v-model="firstName">
    <input type="text" v-model="lastName">
    <p>{{ fullName }}</p>
    <button @click="fullName = 'first last'">change</button>
</template>
<script lang="ts" setup>
import { ref, computed } from 'vue';
const firstName = ref('John')
const lastName = ref('white')
// const fullName = computed(() => {
//     return firstName.value + lastName.value
// })
const fullName = computed({
    get() {
        return firstName.value + ' ' + lastName.value
    },
    set(value) {
        firstName.value = value.split(' ')[0]
        lastName.value = value.split(' ')[1]
    }
})
setTimeout(() => {
    fullName.value = 'first last'
}, 2000)
</script>
```