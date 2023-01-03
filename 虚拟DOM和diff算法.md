### 虚拟DOM

[Vue Template Explorer](https://vue-next-template-explorer.netlify.app/#eyJzcmMiOiI8ZGl2PlxyXG4gICAgPGRpdj4gXHJcbiAgICAgICAgIDxzZWN0aW9uPnRlc3Q8L3NlY3Rpb24+XHJcbiAgICAgIDwvZGl2PiAgXHJcbjwvZGl2PiIsIm9wdGlvbnMiOnt9fQ==)

`虚拟DOM`就是通过JS来生成一个`AST节点树`

[拓展]
*   为什么要有虚拟DOM？
*   通过下面的例子可以发现一个dom上面的属性是非常多的，所以直接操作DOM非常浪费性能
    解决方案就是 我们可以`用JS的计算性能来换取操作DOM所消耗的性能`，既然我们逃不掉操作DOM这道坎,但是我们可以尽可能少的操作DOM
    操作JS是非常快的
    ```js
    let div = document.createElement('div')
    let str = ''
    for (const key in div) {
        str += key + '---'
    }
    console.log(str)
    ```

### diff算法

参考链接： https://zhuanlan.zhihu.com/p/150103393

* patch概念引入

    在`vue update`过程中在遍历子代vnode的过程中，会用不同的patch方法来patch新老vnode，如果找到对应的 newVnode 和 oldVnode,就可以复用利用里面的真实dom节点。避免了重复创建元素带来的性能开销。毕竟浏览器创造真实的dom，操纵真实的dom，性能代价是昂贵的。
    
    patch过程中，如果面对当前vnode存在有很多chidren的情况,那么需要分别遍历patch新的children Vnode和老的 children vnode。

    * element元素类型vnode
        `<ol><li>苹果</li><li>香蕉</li></ol>`
    * flagment碎片类型vnode
        flagment出现就是用看起来像一个普通的DOM元素，但它是虚拟的，根本不会在DOM树中呈现。这样我们可以将组件功能绑定到一个单一的元素中，而不需要创建一个多余的DOM节点。
        `<Fragment><span>苹果</span> <span>香蕉</span></Fragment>`
    children vnode依次向下遍历。那么就需要一个`patchChildren`方法，**依次patch子类vnode**。

* patchChildren
    vue3.0中 在`patchChildren`方法中有这么一段源码
    ```js
    if (patchFlag > 0) {
        if (patchFlag & PatchFlags.KEYED_FRAGMENT) { 
             /* 对于存在key的情况用于diff算法 */
            patchKeyedChildren(
                c1 as VNode[],
                c2 as VNodeArrayChildren,
                container,
                anchor,
                parentComponent,
                parentSuspense,
                isSVG,
                optimized
            )
            return
        } else if (patchFlag & PatchFlags.UNKEYED_FRAGMENT) {
             /* 对于不存在key的情况,直接patch  */
            patchUnkeyedChildren( 
                c1 as VNode[],
                c2 as VNodeArrayChildren,
                container,
                anchor,
                parentComponent,
                parentSuspense,
                isSVG,
                optimized
            )
            return
        }
    }
    ```

* diff算法的作用

    diff作用就是在patch子vnode过程中，找到与新vnode对应的老vnode，复用真实的dom节点，避免不必要的性能开销

    * 不存在key

        ① 比较新老children的length获取最小值 然后**对于公共部分，进行从新patch工作**。
        ② 如果**老节点数量大于新的节点数量 ，移除多出来的节点**。
        ③ 如果**新的节点数量大于老节点的数量，从新 mountChildren新增的节点**。
    * 存在key

* diff算法的过程

    1. 从头遍历 新序列 和 旧序列，有相同的key就 patch
    2. 从尾遍历 新序列 和 旧序列，有相同的key就 patch
    3. 如果1，2步 旧序列 patch完 新序列 还没patch完，直接新增 新序列 剩余元素
    4. 如果1，2步 新序列 patch完 旧序列 还没patch完，直接卸载 旧序列 剩余元素
    5. 如果1，2步后 新旧序列 都没 patch完，使用 map 存储新序列元素及位置，查询 旧序列中可patch 元素
    6. 新增 新序列中未在旧序列找到的元素
    7. 删除 旧序列未在新序列匹配到的元素

* [拓展]新旧序列产生移动的话怎么处理连接？
  * ① 根据 newIndexToOldIndexMap 新老节点索引列表找到最长稳定序列
    ② 对于 newIndexToOldIndexMap -item =0 证明不存在老节点 ，从新形成新的vnode
    ③ 对于发生移动的节点进行移动处理。

* [拓展]有效的key
  * 唯一键。这里的唯一键是数据的uuid。如果使用for循环的index，新旧序列的key值永远是0，1，2，3...，diff算法无法正确的提高性能

    