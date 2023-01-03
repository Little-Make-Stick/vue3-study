## vue3基础语法

* 插值渲染
    插值可以是变量，表达式，函数返回值，条件表达式

    ```vue
    <template>
        <div>
            <p>{{ message }}</p>
            <p>{{ 3 * 8 + 24 }}</p>
            <p>{{ getMessage() }}</p>
            <p>{{ truly ? 'active' : 'inactive' }}</p>
        </div>
    </template>
    <script lang="ts" setup>
    const message : string = 'hello world'
    const getMessage = () => {
        return 'Xiao Yao'
    }
    const truly : boolean = false
    </script>
    ```

* 指令

    * `v-text` 用来显示文本
    * `v-html` 用来展示富文本
    * `v-if` 用来控制元素的显示隐藏（切换真假DOM）
    * `v-else-if` 表示 `v-if` 的“else if 块”。可以链式调用
    * `v-else` `v-if`条件收尾语句
    * `v-show` 用来控制元素的显示隐藏（`display none/block` Css切换）
    * `v-on` 简写`@` 用来给元素添加事件
    * `v-bind` 简写`:`  用来绑定元素的属性Attr
    * `v-model` 双向绑定
    * `v-for` 用来遍历元素
    * `v-on`修饰符 冒泡案例
    * `v-once` 性能优化只渲染一次
    * `v-memo` 性能优化会有缓存

    * `v-on + .stop` 阻止冒泡
        ```vue
        <template>
            <div class="parent" @click.self="()=>{console.log('父元素')}">
                <div class="children" @click.stop="()=>{console.log('子元素')}"></div>
            </div>
        </template>
        ```
    * `v-on + .prevent` 阻止表单默认提交
        ```vue
        <template>
            <form action="/">
                <input type="submit" value="提交" @click.prevent="javascript;"/>
            </form>
        </template>
        ```

    * `v-bind` 动态绑定class
        ```vue
        <template>
            <div :class="classObj"></div>
        </template>
        <script lang="ts" setup>
        type clsType = {
            active: boolean,
            highlight: boolean,
        }
        const classObj : clsType = {
            active: true,
            highlight: true,
        }
        </script>
        ```

    * `v-bind` 动态绑定style
        ```vue
        <template>
            <div :style="styleObj"></div>
        </template>
        <script lang="ts" setup>
        type sylType = {
            height: string,
            width: string,
        }
        const styleObj : sylType = {
            height: '500px',
            width: '100%',
        }
        </script>
        ```

    * `v-model`双向响应
        ```vue
        <template>
            <input type="text" v-model="name"/>
            <div>{{ name }}</div>
        </template>
        <script lang="ts" setup>
        import { ref } from 'vue'
        const name = ref('John')
        </script>
        ```

    * `v-memo`优化响应
        如果一个特定的值发生变化，只需运行更新并重新渲染。
        ```vue
        <template>
            <div v-memo="[slowly]">
                {{ slowly ? 'ooop...' : 'normally'}}
            </div>
            
            <div v-for="item in list" :key="item.index" v-memo='[item.selected]'>
                {{item.index}} -- {{item.name}} -- {{item.selected}} 
                <button @click="item.selected = !item.selected">toggle</button>
            </div>
            <!-- 只关注item.index大于5的那一次即index == 6 -->
            <div v-for="item in list" :key="item.name" v-memo='[item.index > 5]'>
                {{item.index}} -- {{item.name}} -- {{item.selected}} 
                <button @click="item.index++">toggle</button>
            </div>
        </template>
        <script lang="ts" setup>
        import { ref, reactive, watch } from 'vue'
        const slowly:boolean = false;
        type pro = {
            index: number,
            name: string,
            selected: boolean,
        };
        const list: pro[] = reactive([
            {index: 1, name: 'qq', selected: false,},
            {index: 2, name: 'ww', selected: false,},
            {index: 3, name: 'tt', selected: false,},
        ]);
        </script>
        ```

* 计算属性

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

* 监听属性

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

* 生命周期