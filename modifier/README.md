#### 自定义修饰符

#### 组件

```js
const myComponent = {
    template: `<input type="text" :value="modelValue" @input="emitValue" />`,
    props: {
        modelValue: String,
    },
    emits: ['update:modelValue'],
    methods:{
        emitValue(e) {
            let value = e.target.value
            this.$emit('update:modelValue', value)
        }
    }
}
```

#### 父组件中使用

```html
<div id="app">
    <my-component v-model="myText" />
</div>
<script>
const { createApp } = Vue
createApp({
    components: {
        myComponent,
    },
    data() {
        return {
            myText: ''
        }
    },
    watch: {},
    methods: {}
}).mount('#app')
</script>
```


#### 父组件传值时添加修饰器

```html
<my-component v-model.capitalize="myText" />
```


#### 子组件接收修饰器并定义功能

```js
props: {
    modelValue: String,
    // 接收v-model的修饰器
    modelModifiers: {
        default: () => ({})
    }
},
created() {
    // 打印v-model挂载的修饰器
    console.log(this.modelModifiers) // { capitalize: true }
},
methods:{
    emitValue(e) {
        let value = e.target.value
        // 如果v-model 挂载了 `capitalize`
        if (this.modelModifiers.capitalize) {
            // 把首位大写并拼接除首位的剩余字符串
            value = value.charAt(0).toUpperCase() + value.slice(1)
            console.log(value)
        }
        // 把处理后的字符串赋给 modelValue
        this.$emit('update:modelValue', value)
    }
}
```
