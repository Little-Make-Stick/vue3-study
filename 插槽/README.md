## 插槽

插槽一般用于占位，当一个组件框架基本一致，但是某个区域显示风格不定，可以用插槽实现占位，等待实例化插槽再具体实现那一块的内容

### 匿名插槽

`<slot></slot>`有个属性name，用于标识插槽，默认值为 default。
当使用 slot 且不提供 name 属性，此插槽为匿名插槽，插槽名字默认为 default

* CompA
    ``vue
    <template>
       <p>Component A</p>
       <slot></slot>
    </template>
    ``
* 实例化插槽
    ```vue
    <CompA>
        <template>
            <p>匿名插槽的内容</p>
        </template>
    </CompA>
    ```

### 具名插槽

当为`<slot>`的name属性提供值，此时插槽为具名插槽
实例化具名插槽可以通过 `v-slot:desc` ,也可以简写成 `#desc`
插槽内部可以提供一个默认展示，如此当使用组件并没有提供插槽实例化就会默认展示组件插槽的内容

* CompA
    ```vue
    <template>
        <p>Component A</p>
        <slot name="desc">
            <p>desc</p>
        </slot>
    </template>
    ```
* 实例化插槽
    ```vue
    <CompA>
        <template #desc>
            <p>匿名插槽的内容</p>
        </template>
    </CompA>
    ```

### 插槽传参

创建插槽时可以通过 `v-bind:参数名="参数值"`传参,也可以简写成`:参数名="参数值"`
此时接收参数是以 `#插槽名="所有参数组成的对象"`,当`想直接具体拿到某个参数可以通过解构`

* CompA
    ``vue
    <template>
       <p>{{ mime.name }}-{{ mime.age }}</p>
       <slot name="hobbies" :hobbies="mime.hobbies" ></slot>
    </template>
    <script lang="ts" setup>
        type myProfit = {
            name: string,
            age: number,
            hobbies: string[]
        }
        defineProps<{mime: myProfit}>()
    </script>
    ``
* 实例化插槽
    ```vue
    <CompA>
        <template #hobbies="{hobbies}">
            <ul>
                <li v-for="item in hobbies" :key="item">{{ item }}</li>
            </ul>
        </template>
    </CompA>
    <script lang="ts" setup>
    import {ref} from 'vue'
    type myProfit = {
        name: string,
        age: number,
        hobbies: string[]
    }
    const mime = ref({
      name: 'John',
      age: 18,
      hobbies: ['run', 'jump']
    })
</script>
    ```

### 动态插槽

使用`v-slot:[变量名]`或者 `#[变量名]`设置动态插槽，修改变量名时可以变换实例化的插槽对象
适用于页面间多个插槽，通过切换实例化的插槽变换模块的位置

* CompA
    ```vue
    <div class="comp-a" >
        <p>{{ mime.name }}</p>
        <slot name="age" :age="mime.age">
        </slot>
        <slot name="hobbies" v-bind:hobbies="mime.hobbies">
        </slot>
    </div>
    <script lang="ts" setup>
        type myProfit = {
            name: string,
            age: number,
            hobbies: string[]
        }
        defineProps<{mime: myProfit}>()
    </script>
    ``
* 实例化插槽
    ```vue
    <SlotComp :mime="mime" >
      <template v-slot:[_dynamic]="slotProps">
        <ol>
          <li v-for="(value, key) in slotProps" :key="key">{{ key }}-{{ value }}</li>
        </ol>
      </template>
    </SlotComp>
    <button @click="_dynamic = _dynamic == 'age' ? 'hobbies' : 'age'">switchSlot</button>
    <script lang="ts" setup>
    import {ref} from 'vue'
    type myProfit = {
        name: string,
        age: number,
        hobbies: string[]
    }
    const mime = ref({
      name: 'John',
      age: 18,
      hobbies: ['run', 'jump']
    })
    const _dynamic = ref('age')
</script>
    ```


