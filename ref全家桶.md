### ref

将变量转化成响应式（深层次响应）
`html的ref声明`配合`同名同类型变量`获取dom元素

```vue
<template>
    <input v-model="mime.account"/>
    <p ref="dom">{{ mime.account }}</p>
</template>
<script lang="ts" setup>
    import { ref } from 'vue'
    const mime = ref({ account: 'xiao yao' })
    const dom = = ref<HTMLDivElement>()
    setTimeout(()=>{
        console.log(dom.value);
    }, 2000)
</script>
```

### Ref

ref声明

```vue
<template>
    <input v-model="name"/>
    <p>{{ name }}</p>
</template>
<script lang="ts" setup>
    import { ref, Ref } from 'vue'
    const name: Ref<string> = ref('xiao yao')
</script>
```

### isRef

判断一个变量是否ref

```vue
<template>
    <input v-model="name"/>
    <p>{{ name }}</p>
</template>
<script lang="ts" setup>
    import { ref, isRef } from 'vue'
    const name = ref('xiao yao')
    console.log(isRef( name ))
</script>
```

### toRef

提取响应式引用类型的某个属性并设成ref。
* 不管是修改原来对象属性值(mime.name)，还是修改提取出来的变量(myName)，两者都会一起响应

```vue
<template>
    <input v-model="mime.name" />
    <p>{{  mime }}</p>
    <p>{{ myName }}</p>
</template>
<script lang="ts" setup>
    import { toRef, reactive } from 'vue'

    const mime = reactive({name: 'xiao yao'})
    const myName = toRef(mime, 'name')
    console.log(mime, myName);
</script>
```

### toRefs

提取响应式引用类型的所有属性值并都设为ref
* 不管是修改原来对象属性值(mime.name)，还是修改提取出来的变量(myName)，两者都会一起响应
* 可以通过解构一次性拿出来所有的属性，并且可以定义别名

```vue
<template>
    <input v-model="mime.name" />
    <p>{{  mime }}</p>
    <p>{{ myName }}--{{ age }}</p>
</template>
<script lang="ts" setup>
    import { toRefs, reactive } from 'vue'

    const mime = reactive({name: 'xiao yao', age: 18})
    const {name: myName, age} = toRefs(mime)
    console.log(mime, myName, age);
</script>
```

### unref

### shallowRef

将变量转化成响应式（浅层次响应），创建一个跟踪自身 `.value` 变化的 ref

```vue
<template>
    <input v-model="mime.account"/>
    <p>{{ mime.account }}</p>
</template>
<script lang="ts" setup>
    import { shallowRef } from 'vue'
    const mime = shallowRef({ account: 'xiao yao' })
</script>
```

### triggerRef 

强制更新收集到的依赖

```vue
<template>
    <input v-model="mime.account" @change="handlerChange"/>
    <p>{{ mime.account }}</p>
</template>
<script lang="ts" setup>
    import { shallowRef, triggerRef } from 'vue'
    const mime = shallowRef({ account: 'xiao yao' })
    const handlerChange = (e: Event) => {
        triggerRef(mime)
    }
</script>
```

### customRef

自定义shallowRef，用于给响应式 添加一些个性化操作

```vue
<template>
    <input v-model="mime" />
    <p>{{ mime }}</p>
</template>
<script lang="ts" setup>
    import { customRef } from 'vue' 
    function MyRef<T>(value: T) {
        let timeoutId = 0;
        return customRef((track, trigger) => {
            return {
                get() {
                    // 收集依赖
                    track();
                    return value;
                },
                set(newVal) {
                    timeoutId && clearTimeout(timeoutId)
                    timeoutId = setTimeout(()=>{
                        console.log(newVal);
                        value = newVal;
                        timeoutId = 0;
                        // 触发依赖
                        trigger();
                    }, 1000)
                }
            }
        })
    }
    const mime = MyRef<string>('xiao yao')
</script>
```

### proxyRefs

### 拓展

* [拓展] ref 和 shallowRef 的区别？
    1. 从响应看，ref 生成的响应可以监听到 变量的属性变化，shallowRef 只能监听到自身 `.value` 的变化
    2. 从源码看，shallowRef创建时返回的是 value ，ref返回的是 toRef()

* [拓展] ref 和 shallowRef 不能一起写
    如果 ref 和 shallowRef 的变量在一个作用区间一起改变值，那么 shallowRef 会被影响，造成视图的更新

### TODO

TODO: unref, proxyRefs