## 选项式API组件传参

### 父传子

* 父组件
    ```vue
    <template>
        <CompA title="标题aaa"></CompA>
    </template>
    <script setup>
    import CompA from '../components/CompA.vue'
    </script>
    ```
* 子组件
  1. 使用 `defineProps` 接收父组件传过来的参数，返回一个 props 实例，可以通过访问这个实例的属性访问具体参数
  2. 父组件传过来的参数是只读的，不支持修改
    ```vue
    <template>
        <div class="comp-a">
            <p>{{ title }}</p>
        </div>
    </template>
    <script setup>
    const props = defineProps({
        title: {
            type: String,
            default: '标题'
        }
    })
    console.log(props.title)
    </script>
    ```
    * js写法
        ```js
        const props = defineProps({
            title: {
                type: String,
                default: '标题'
            }
        })
        ```
    * ts写法
        ```ts
        type propsType = {
            title: string,
        }
        // 不带默认值的写法
        const props = defineProps<propsType>()
        ```
        如果需要配置默认值则需要借助 withDefaults
        ```ts
        const props = withDefaults(defineProps<{
            title: string
        }>(), {
            title: "标题"
        })
        ```

### 子传父

父组件给子组件传递一个需要参数的事件，子组件收到事件并通过defineEmits注册，返回一个事件处理器函数，在适当的时刻可以通过该事件处理器触发注册的事件并传参

* 父组件
    ```vue
    <template>
        <CompA @custom-event="receiveMsg"></CompA>
    </template>
    <script setup>
    import CompA from '../components/CompA.vue'
    const receiveMsg = (msg: string) => {
        console.log('收到子组件的消息', msg);
    }
    </script>
    ```
* 子组件
    ```vue
    <template>
        <div class="comp-a">
            <button @click="sendToParent">给父组件传参</button>
        </div>
    </template>
    <script setup>
    const emit = defineEmits(['customEvent'])
    const sendToParent = () => {
        emit('customEvent', '我是子组件的消息')
    }
    </script>
    ```

### 父组件使用子组件的属性

vue3的组合式API不能直接访问到子组件的属性，只能访问子组件通过defineExpose暴露出来的属性
子组件暴露出来的属性如果本身是响应式暴露出来依旧是响应式，并且可写

* 父组件
    ```vue
    <template>
        <CompA ref="myCompA" ></CompA>
        <button @click="readList">读取compA暴露的属性list</button>
    </template>
    <script setup>
    import CompA from '../components/CompA.vue'
    import { ref } from 'vue'

    const myCompA = ref();
    const readList = () => {
        console.log('读取compA暴露的属性list', myCompA.value.list);
        myCompA.value.list.push(6)
    }
    </script>
    ```
* 子组件
    ```vue
    <template>
        <div class="comp-a">compA</div>
    </template>
    <script setup>
    import { reactive } from 'vue'
    const list = reactive([2, 4, 6])
    defineExpose({
        list,
    })
    </script>
    ```

## 组合式API组件传参

### 父传子

子组件接收参数和 vue2 一样，不过新增了在 setup的参数也可以接收 props，方便在 setup 里拿到参数去操作

* 父组件
    ```vue
    <template>
        <CompA title="标题aaa"></CompA>
    </template>
    <script setup>
    import CompA from '../components/CompA.vue'
    </script>
    ```
* 子组件
    ```vue
    <template>
        <div class="comp-a">
            <p>{{ title }}</p>
        </div>
    </template>
    <script>
    export default {
        props: {
            title: String
        },
        setup(props, context) {
            console.log(props.title)
            return {}
        }
    }
    </script>
    ```


### 子传父

子组件给父组件传参依旧是`通过 emit事件派发`，不过因为`在 setup 访问不到 this`，只能通过 `setup的参数上下文对象(context) 访问 emit`

* 父组件
    ```vue
    <template>
        <CompA @custom-event="receiveMsg"></CompA>
    </template>
    <script setup>
    import CompA from '../components/CompA.vue'
    const receiveMsg = (msg: string) => {
        console.log('收到子组件的消息', msg);
    }
    </script>
    ```
* 子组件
    ```vue
    <template>
        <div class="comp-a">
            <button @click="sendToParent">给父组件传参</button>
        </div>
    </template>
    <script>
    export default {
        setup(props, context) {
            const sendToParent = () => {
                context.emit('customEvent', '我是子组件的消息')
            }
            return {
                sendToParent
            }
        }
    }
    </script>
    ```

### 父组件使用子组件的属性

因为`在 setup 访问不到 this`，只能通过 `setup的参数上下文对象(context) 访问 expose` 给父组件暴露属性
这里在 选项式API的setup通过ref取同名DOM 时记得在`setup` return出来，不然会出现 undefined

* 父组件
    ```vue
    <template>
        <CompA ref="myCompA" ></CompA>
        <button @click="readList">读取compA暴露的属性list</button>
    </template>
    <script>
    import CompA from '../components/CompA.vue'
    import { ref } from 'vue'
    export default {
        components: { CompA, },
        setup(props, context) {
            const myCompA = ref();
            const readList = () => {
                console.log('读取compA暴露的属性list', myCompA.value.list);
                myCompA.value.list.push(6)
            }
            return {
                myCompA,
                readList
            }
        }
    }
    </script>
    ```
* 子组件
    ```vue
    <template>
        <div class="comp-a">list</div>
    </template>
    <script>
    export default {
        setup(props, context) {
            const list = reactive([2, 4, 6])
            context.expose({list})
            return {
                list
            }
        }
    }
    </script>
    ```


### 拓展

* vue2 vue3在组件传参上的区别
  1. vue3里面 `父组件不能直接访问子组件的属性`，当需要访问甚至改变子组件属性时，需要子组件通过defineExpose暴露该属性
  2. 在 vue3的组合式API中不存在this，所以父组件传过来的参数需要在子组件通过defineProps手动接收
  3. 在 vue3的组合式API中不存在this，所以子组件需要通过defineEmits手动接收父组件的派发事件