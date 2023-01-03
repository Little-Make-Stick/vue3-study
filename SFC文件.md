### SFC 语法规范

*.vue 件都由三种类型的顶层语法块所组成：`<template>`、`<script>`、`<style>`

* `<template>`
每个 `*.vue` 文件**最多可同时包含一个顶层 `<template>` 块**。
相较于 `vue2` , **`<template>`下可以有多个元素**，而不用基于一个元素

其中的内容会被提取出来并传递给 `@vue/compiler-dom`，预编译为 `JavaScript` 的渲染函数，并附属到导出的组件上作为其 `render` 选项。

* `<script>`
每一个 `*.vue` 文件可以有多个 `<script>` 块 (不包括`<script setup>`)。

该脚本将作为 `ES Module` 来执行。

其默认导出的内容应该是 `Vue` 组件选项对象，它要么是一个普通的对象，要么是 `defineComponent` 的返回值。

 * `<script setup>`
   每个 `*.vue` 文件**最多只能有一个 `<script setup>` 块** (不包括常规的 `<script>`)
   
   该脚本会被预处理并作为组件的 setup() 函数使用，也就是说它会在每个组件实例中执行。`<script setup>` 的顶层绑定会自动暴露给模板。更多详情请查看 `<script setup>` 文档。

* `<style>`
一个 *.vue 文件可以包含多个 <style> 标签。

`<style>` 标签可以通过 scoped 或 module attribute (更多详情请查看 SFC 样式特性) 将样式封装在当前组件内。多个不同封装模式的 `<style>` 标签可以在同一个组件中混

### vite包三个文件

* vite
    `unix Linux macOS` 系默认的可执行文件，必须输入完整文件名

* vite.cmd
    `windows cmd` 中默认的可执行文件，当我们不添加后缀名时，自动根据 `pathext` 查找文件

* vite.ps1
    `Windows PowerShell` 中可执行文件，可以跨平台