aDiff算法比较的就是虚拟DOM的差异

## 1、认识虚拟DOM

用`JS`对象模拟`DOM`结构。

```html
<!-- 转换前 -->
<template>
    <div id="app" class="container">
        <h1>沐华</h1>
    </div>
</template>
```

```js
// 转换为虚拟DOM后，简称vnode（virtual Node）
{
  tag:'div',
  props:{ id:'app', class:'container' },
  children: [
    { tag: 'h1', children:'沐华' }
  ]
}
```

每一个标签都转为一个对象，该对象有三个属性`tag`、`props`和`children`

- tag：必选，标签，也可以是组件或者函数；
- props：非必选，标签上的属性和方法；
- children：非必选，标签的内容或者子节点，如果是文本节点就是字符串，如果有子节点就是数组；

### （1）使用虚拟DOM的原因是

![image.png](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303121751621.awebp)

真实DOM有非常多的属性和事件，而使用虚拟 DOM 来提升性能的点在于 DOM 发生变化的时候，通过 diff 算法和数据改变前的 DOM 对比，计算出需要更改的 DOM，然后只对变化的 DOM 进行操作，而不是更新整个视图。

在 Vue 里虚拟 DOM 的数据更新机制采用的是异步更新队列，就是把变更后的数据装入一个数据变更新的异步队列，就是 `patch`，用它来做新老 vnode 对比。



## 2、认识Diff算法

通过新旧虚拟DOM对比（即patch的过程），<font color=deepred>**找出最小变化的地方**</font>进行DOM操作。

### （1）执行时机

1. 页面首次渲染时：调用一次`patch`并创建新的`vnode`，不会进行深层次比较。
2. 组件中数据发生变化时：会触发`setter`然后通过`Notify`通知`Watcher`，对应的`Watcher`通知并执行更新函数，会执行`render`函数获取新的虚拟DOM，然后执行`patch`对比上次渲染结果的老的虚拟DOM，并计算出最小的变化，然后根据最小的变化去更新真实DOM，即视图更新。



### （2）对比计算过程

![image-20230312182353760](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303121823250.png)

计算上图DOM结构变化过程：

1. 遍历老的虚拟DOM；
2. 遍历新的虚拟DOM；
3. 根据改变和新增，再重新排序；

但是存在很大的问题，性能消耗太大，所以Vue和React里使用DIff算法都遵循深度优先，同层比较的策略做了优化，计算最小变化。



### （3）DIff算法的优化

#### 3.1、只比较同一层级，不跨级比较

![image-20230314201602441](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303142016570.png)

Diff过程只会把同颜色框起来同一层级的DOM进行比较；



#### 3.2、比较标签名

如果同一层级的比较标签名不同，就直接移除老的虚拟 DOM 对应的节点，不继续按这个树状结构做深度比较。

![image-20230314220919386](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303142209736.png)



#### 3.3、比较key

标签名相同，key也相同，就会认为是相同节点，不继续深度比较。



#### 拓展：key的作用

1. 高效的更新虚拟DOM，非常精准的找到相同节点，patch过程会非常高效；
2. 避免使用数组下标作为 key，因为 key 值不是唯一的话可能会导致出现bug；
3. Vue 判断两个节点是否相同时主要判断两者的元素类型和 key 等，如果不设置 key，就可能永远认为这两个是相同节点，只能去做更新操作，造成大量不必要的 DOM 更新操作，影响性能；



## 3、源码

### （1）patch

patch是一个函数，接收四个参数：

- oldVnode：老的虚拟DOM节点；
- vnode：新的虚拟DOM节点；
- hydrating：是否和真实DOM混合，服务端渲染用；
- removeOnly：transition-group 会用到；



主要流程：

1. vnode 不存在，oldVnode 存在，就删掉 oldVnode；
2. vnode 存在，oldVnode 不存在，就创建 vnode；
3. 两个都存在的话，通过 sameVnode 函数对比是不是同一节点；
   1. 是同一节点，通过 patchVnode 进行后续对比节点文本变化或子节点变化；
   2. 不是同一节点，就把 vnode 挂载到 oldVnode 的父元素下；
      1. 如果组件的根节点被替换，就遍历更新父节点，然后删掉旧的节点；
      2. 如果是服务端渲染就用 hydrating 把 oldVnode 和真实 DOM 混合；

patch函数源码：

```js
// 两个判断函数
function isUndef (v: any): boolean %checks {
  return v === undefined || v === null
}
function isDef (v: any): boolean %checks {
  return v !== undefined && v !== null
}

return function patch (oldVnode, vnode, hydrating, removeOnly) {
  // 如果新的 vnode 不存在，但是 oldVnode 存在
  if (isUndef(vnode)) {
    // 如果 oldVnode 存在，调用 oldVnode 的组件卸载钩子 destroy
    if (isDef(oldVnode)) invokeDestroyHook(oldVnode)
    return
  }

  let isInitialPatch = false
  const insertedVnodeQueue = []

  // 如果 oldVnode 不存在的话，新的 vnode 是肯定存在的，比如首次渲染的时候
  if (isUndef(oldVnode)) {
    isInitialPatch = true
    // 就创建新的 vnode
    createElm(vnode, insertedVnodeQueue)
  } else {
    // 剩下的都是新的 vnode 和 oldVnode 都存在的话

    // 是不是元素节点
    const isRealElement = isDef(oldVnode.nodeType)
    // 是元素节点 && 通过 sameVnode 对比是不是同一个节点 (函数后面有详解)
    if (!isRealElement && sameVnode(oldVnode, vnode)) {
      // 如果是 就用 patchVnode 进行后续对比 (函数后面有详解)
      patchVnode(oldVnode, vnode, insertedVnodeQueue, null, null, removeOnly)
    } else {
      // 如果不是同一元素节点的话
      if (isRealElement) {
        // const SSR_ATTR = 'data-server-rendered'
        // 如果是元素节点 并且有 'data-server-rendered' 这个属性
        if (oldVnode.nodeType === 1 && oldVnode.hasAttribute(SSR_ATTR)) {
          // 就是服务端渲染的，删掉这个属性
          oldVnode.removeAttribute(SSR_ATTR)
          hydrating = true
        }
        // 这个判断里是服务端渲染的处理逻辑，就是混合
        if (isTrue(hydrating)) {
          if (hydrate(oldVnode, vnode, insertedVnodeQueue)) {
            invokeInsertHook(vnode, insertedVnodeQueue, true)
            return oldVnode
          } else if (process.env.NODE_ENV !== 'production') {
            warn('这是一段很长的警告信息')
          }
        }
        // function emptyNodeAt (elm) {
        //    return new VNode(nodeOps.tagName(elm).toLowerCase(), {}, [], undefined, elm)
        //  }
        // 如果不是服务端渲染的，或者混合失败，就创建一个空的注释节点替换 oldVnode
        oldVnode = emptyNodeAt(oldVnode)
      }

      // 拿到 oldVnode 的父节点
      const oldElm = oldVnode.elm
      const parentElm = nodeOps.parentNode(oldElm)

      // 根据新的 vnode 创建一个 DOM 节点，挂载到父节点上
      createElm(
        vnode,
        insertedVnodeQueue,
        oldElm._leaveCb ? null : parentElm,
        nodeOps.nextSibling(oldElm)
      )

      // 如果新的 vnode 的根节点存在，就是说根节点被修改了，就需要遍历更新父节点
      if (isDef(vnode.parent)) {
        let ancestor = vnode.parent
        const patchable = isPatchable(vnode)
        // 递归更新父节点下的元素
        while (ancestor) {
          // 卸载老根节点下的全部组件
          for (let i = 0; i < cbs.destroy.length; ++i) {
            cbs.destroy[i](ancestor)
          }
          // 替换现有元素
          ancestor.elm = vnode.elm
          if (patchable) {
            for (let i = 0; i < cbs.create.length; ++i) {
              cbs.create[i](emptyNode, ancestor)
            }
            const insert = ancestor.data.hook.insert
            if (insert.merged) {
              for (let i = 1; i < insert.fns.length; i++) {
                insert.fns[i]()
              }
            }
          } else {
            registerRef(ancestor)
          }
          // 更新父节点
          ancestor = ancestor.parent
        }
      }
      // 如果旧节点还存在，就删掉旧节点
      if (isDef(parentElm)) {
        removeVnodes([oldVnode], 0, 0)
      } else if (isDef(oldVnode.tag)) {
        // 否则直接卸载 oldVnode
        invokeDestroyHook(oldVnode)
      }
    }
  }
  // 返回更新后的节点
  invokeInsertHook(vnode, insertedVnodeQueue, isInitialPatch)
  return vnode.elm
}
```



### （2）sameVnode

判断是不是同一节点的函数

```js
function sameVnode (a, b) {
  return (
    a.key === b.key &&  // key 是不是一样
    a.asyncFactory === b.asyncFactory && ( // 是不是异步组件
      (
        a.tag === b.tag && // 标签是不是一样
        a.isComment === b.isComment && // 是不是注释节点
        isDef(a.data) === isDef(b.data) && // 内容数据是不是一样
        sameInputType(a, b) // 判断 input 的 type 是不是一样
      ) || (
        isTrue(a.isAsyncPlaceholder) && // 判断区分异步组件的占位符否存在
        isUndef(b.asyncFactory.error)
      )
    )
  )
}
```



### （3）patchVnode

新的 vnode 和 oldVnode 是同一节点的情况下，才会执行的函数，主要是对比节点文本变化或子节点变化。

- 如果 oldVnode 和 vnode 的引用地址是一样的，就表示节点没有变化，直接返回；
- 如果 oldVnode 的 isAsyncPlaceholder 存在，就跳过异步组件的检查，直接返回；
- 如果 oldVnode 和 vnode 都是静态节点，并且有一样的 key，并且 vnode 是克隆节点或者 v-once 指令控制的节点时，把 oldVnode.elm 和 oldVnode.child 都复制到 vnode 上，然后返回；
- 如果 vnode 不是文本节点也不是注释的情况下：
  - 如果 vnode 和 oldVnode 都有子节点，而且子节点不一样的话，就调用 updateChildren 更新子节点；
  - 如果只有 vnode 有子节点，就调用 addVnodes 创建子节点；
  - 如果只有 oldVnode 有子节点，就调用 removeVnodes 删除该子节点；
  - 如果 vnode 文本为 undefined，就删掉 vnode.elm 文本；
- 如果 vnode 是文本节点但是和 oldVnode 文本内容不一样，就更新文本

```js
function patchVnode (
oldVnode, // 老的虚拟 DOM 节点
 vnode, // 新的虚拟 DOM 节点
 insertedVnodeQueue, // 插入节点的队列
 ownerArray, // 节点数组
 index, // 当前节点的下标
 removeOnly
) {
  // 新老节点引用地址是一样的，直接返回
  // 比如 props 没有改变的时候，子组件就不做渲染，直接复用
  if (oldVnode === vnode) return

  // 新的 vnode 真实的 DOM 元素
  if (isDef(vnode.elm) && isDef(ownerArray)) {
    // clone reused vnode
    vnode = ownerArray[index] = cloneVNode(vnode)
  }

  const elm = vnode.elm = oldVnode.elm
  // 如果当前节点是注释或 v-if 的，或者是异步函数，就跳过检查异步组件
  if (isTrue(oldVnode.isAsyncPlaceholder)) {
    if (isDef(vnode.asyncFactory.resolved)) {
      hydrate(oldVnode.elm, vnode, insertedVnodeQueue)
    } else {
      vnode.isAsyncPlaceholder = true
    }
    return
  }
  // 当前节点是静态节点的时候，key 也一样，或者有 v-once 的时候，就直接赋值返回
  if (isTrue(vnode.isStatic) &&
      isTrue(oldVnode.isStatic) &&
      vnode.key === oldVnode.key &&
      (isTrue(vnode.isCloned) || isTrue(vnode.isOnce))
     ) {
    vnode.componentInstance = oldVnode.componentInstance
    return
  }
  // hook 相关的不用管
  let i
  const data = vnode.data
  if (isDef(data) && isDef(i = data.hook) && isDef(i = i.prepatch)) {
    i(oldVnode, vnode)
  }
  // 获取子元素列表
  const oldCh = oldVnode.children
  const ch = vnode.children

  if (isDef(data) && isPatchable(vnode)) {
    // 遍历调用 update 更新 oldVnode 所有属性，比如 class,style,attrs,domProps,events...
    // 这里的 update 钩子函数是 vnode 本身的钩子函数
    for (i = 0; i < cbs.update.length; ++i) cbs.update[i](oldVnode, vnode)
    // 这里的 update 钩子函数是我们传过来的函数
    if (isDef(i = data.hook) && isDef(i = i.update)) i(oldVnode, vnode)
  }
  // 如果新节点不是文本节点，也就是说有子节点
  if (isUndef(vnode.text)) {
    // 如果新老节点都有子节点
    if (isDef(oldCh) && isDef(ch)) {
      // 如果新老节点的子节点不一样，就执行 updateChildren 函数，对比子节点
      if (oldCh !== ch) updateChildren(elm, oldCh, ch, insertedVnodeQueue, removeOnly)
    } else if (isDef(ch)) {
      // 如果新节点有子节点的话，就是说老节点没有子节点

      // 如果老节点文本节点，就是说没有子节点，就清空
      if (isDef(oldVnode.text)) nodeOps.setTextContent(elm, '')
      // 添加子节点
      addVnodes(elm, null, ch, 0, ch.length - 1, insertedVnodeQueue)
    } else if (isDef(oldCh)) {
      // 如果新节点没有子节点，老节点有子节点，就删除
      removeVnodes(oldCh, 0, oldCh.length - 1)
    } else if (isDef(oldVnode.text)) {
      // 如果老节点是文本节点，就清空
      nodeOps.setTextContent(elm, '')
    }
  } else if (oldVnode.text !== vnode.text) {
    // 新老节点都是文本节点，且文本不一样，就更新文本
    nodeOps.setTextContent(elm, vnode.text)
  }
  if (isDef(data)) {
    // 执行 postpatch 钩子
    if (isDef(i = data.hook) && isDef(i = i.postpatch)) i(oldVnode, vnode)
  }
}
```



### （4）updateChildren

新的 vnode 和 oldVnode 都有子节点，且子节点不一样的时候进行对比子节点的函数。

循环遍历新旧两个字节点，遵循以下循环规则：

1. 新的头和老的头对比；
2. 新的尾和老的尾对比；
3. 新的头和老的尾对比；
4. 新的尾和老的头对比；

![diff2.gif](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303152038827.awebp)

上面四种情况，只要有一种判断相等，就会调用patchVnode对比节点文本变化或子节点变化，然后移动对比的下标，继续下一轮循环对比。

如果都没有命中，就不断用新开始节点的key去老的children中寻找

- 如果没找到，就创建一个新的节点；
- 如果找到了，再对比标签是不是同一个节点
  - 如果是同一个节点，就调用 patchVnode 进行后续对比，然后把这个节点插入到老的开始前面，并且移动新的开始下标，继续下一轮循环对比；
  - 如果不是相同节点，就创建一个新的节点；
- 如果老的 vnode 先遍历完，就添加新的 vnode 没有遍历的节点；
- 如果新的 vnode 先遍历完，就删除老的 vnode 没有遍历的节点；

```js
function updateChildren (parentElm, oldCh, newCh, insertedVnodeQueue, removeOnly) {
  let oldStartIdx = 0 // 老 vnode 遍历的下标
  let newStartIdx = 0 // 新 vnode 遍历的下标
  let oldEndIdx = oldCh.length - 1 // 老 vnode 列表长度
  let oldStartVnode = oldCh[0] // 老 vnode 列表第一个子元素
  let oldEndVnode = oldCh[oldEndIdx] // 老 vnode 列表最后一个子元素
  let newEndIdx = newCh.length - 1 // 新 vnode 列表长度
  let newStartVnode = newCh[0] // 新 vnode 列表第一个子元素
  let newEndVnode = newCh[newEndIdx] // 新 vnode 列表最后一个子元素
  let oldKeyToIdx, idxInOld, vnodeToMove, refElm

  const canMove = !removeOnly

  // 循环，规则是开始指针向右移动，结束指针向左移动移动
  // 当开始和结束的指针重合的时候就结束循环
  while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
    if (isUndef(oldStartVnode)) {
      oldStartVnode = oldCh[++oldStartIdx] // Vnode has been moved left
    } else if (isUndef(oldEndVnode)) {
      oldEndVnode = oldCh[--oldEndIdx]

      // 老开始和新开始对比
    } else if (sameVnode(oldStartVnode, newStartVnode)) {
      // 是同一节点 递归调用 继续对比这两个节点的内容和子节点
      patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
      // 然后把指针后移一位，从前往后依次对比
      // 比如第一次对比两个列表的[0]，然后比[1]...，后面同理
      oldStartVnode = oldCh[++oldStartIdx]
      newStartVnode = newCh[++newStartIdx]

      // 老结束和新结束对比
    } else if (sameVnode(oldEndVnode, newEndVnode)) {
      patchVnode(oldEndVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx)
      // 然后把指针前移一位，从后往前比
      oldEndVnode = oldCh[--oldEndIdx]
      newEndVnode = newCh[--newEndIdx]

      // 老开始和新结束对比
    } else if (sameVnode(oldStartVnode, newEndVnode)) { // Vnode moved right
      patchVnode(oldStartVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx)
      canMove && nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm))
      // 老的列表从前往后取值，新的列表从后往前取值，然后对比
      oldStartVnode = oldCh[++oldStartIdx]
      newEndVnode = newCh[--newEndIdx]

      // 老结束和新开始对比
    } else if (sameVnode(oldEndVnode, newStartVnode)) { // Vnode moved left
      patchVnode(oldEndVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
      canMove && nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm)
      // 老的列表从后往前取值，新的列表从前往后取值，然后对比
      oldEndVnode = oldCh[--oldEndIdx]
      newStartVnode = newCh[++newStartIdx]

      // 以上四种情况都没有命中的情况
    } else {
      if (isUndef(oldKeyToIdx)) oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx)
      // 拿到新开始的 key，在老的 children 里去找有没有某个节点有这个 key
      idxInOld = isDef(newStartVnode.key)
        ? oldKeyToIdx[newStartVnode.key]
      : findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx)

      // 新的 children 里有，可是没有在老的 children 里找到对应的元素
      if (isUndef(idxInOld)) {
        /// 就创建新的元素
        createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx)
      } else {
        // 在老的 children 里找到了对应的元素
        vnodeToMove = oldCh[idxInOld]
        // 判断标签如果是一样的
        if (sameVnode(vnodeToMove, newStartVnode)) {
          // 就把两个相同的节点做一个更新
          patchVnode(vnodeToMove, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
          oldCh[idxInOld] = undefined
          canMove && nodeOps.insertBefore(parentElm, vnodeToMove.elm, oldStartVnode.elm)
        } else {
          // 如果标签是不一样的，就创建新的元素
          createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx)
        }
      }
      newStartVnode = newCh[++newStartIdx]
    }
  }
  // oldStartIdx > oldEndIdx 说明老的 vnode 先遍历完
  if (oldStartIdx > oldEndIdx) {
    // 就添加从 newStartIdx 到 newEndIdx 之间的节点
    refElm = isUndef(newCh[newEndIdx + 1]) ? null : newCh[newEndIdx + 1].elm
    addVnodes(parentElm, refElm, newCh, newStartIdx, newEndIdx, insertedVnodeQueue)

    // 否则就说明新的 vnode 先遍历完
  } else if (newStartIdx > newEndIdx) {
    // 就删除掉老的 vnode 里没有遍历的节点
    removeVnodes(oldCh, oldStartIdx, oldEndIdx)
  }
}
```



## 4、Vue3优化

 Vue3 里整个重写了 Diff 算法，源码基本是完全**不一样**的。

### （1）事件缓存

将事件缓存，可以理解为变成静态的

举例说明：

```vue
<button @click="handleClick">按钮</button>
```

经过Vue3编译过后的结果

```js
export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createElementBlock("button", {
    onClick: _cache[0] || (_cache[0] = (...args) => (_ctx.handleClick && _ctx.handleClick(...args)))
  }, "按钮"))
}
```

其中onClick事件首先读取缓存，如果没有缓存就把传入的事件存储到缓存中。



### （2）添加静态标记

Vue2 是全量 Diff，Vue3 是静态标记 + 非全量 Diff

```js
export const enum PatchFlags {
  TEXT = 1 ,  // 动态文本节点
  CLASS = 1 << 1,  // 2   动态class
  STYLE = 1 << 2,  // 4   动态style
  PROPS = 1 << 3,  // 8   除去class/style以外的动态属性
  FULL_PROPS = 1 << 4,       // 16  有动态key属性的节点，当key改变时，需进行完整的diff比较
  HYDRATE_EVENTS = 1 << 5,   // 32  有监听事件的节点
  STABLE_FRAGMENT = 1 << 6,  // 64  一个不会改变子节点顺序的fragment (一个组件内多个根元素就会用fragment包裹)
  KEYED_FRAGMENT = 1 << 7,   // 128 带有key属性的fragment或部分子节点有key
  UNKEYEN_FRAGMENT = 1 << 8, // 256  子节点没有key的fragment
  NEED_PATCH = 1 << 9,       // 512  一个节点只会进行非props比较
  DYNAMIC_SLOTS = 1 << 10,   // 1024   动态slot
  HOISTED = -1,  // 静态节点 
  BAIL = -2      // 表示 Diff 过程中不需要优化
}
```

举例说明：

```vue
<div id="app">
    <div>静态节点</div>
    <p>{{ name }}</p>
</div>
```

使用Vue2编译过后的结果

```js
with(this){
    return _c(
      'div',
      {attrs:{"id":"app"}},
      [ 
        _c('div',[_v("静态节点")]),
        _c('p',[_v(_s(age))])
      ]
    )
}
```

使用Vue3编译过后的结果

```js
const _hoisted_1 = { id: "app" }
const _hoisted_2 = /*#__PURE__*/_createElementVNode("div", null, "静态节点", -1 /* HOISTED */)

export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createElementBlock("div", _hoisted_1, [
    _hoisted_2,
    _createElementVNode("p", null, _toDisplayString(_ctx.age), 1 /* TEXT */)
  ]))
}
```

其中1和-1就是静态标记，patch过程中会根据这个属性跳过一些静态节点对比



### （3）静态提升

创建静态节点时保存，后续直接复用

 Vue3 中会把不参与更新的元素保存起来，只创建一次，之后在每次渲染的时候不停地复用



### （4）patchKeyedChildren使用最长递增子序列优化了对比流程

Vue2 里在 updateChildren() 函数里对比变更，在 Vue3 主要在 patchKeyedChildren() 函数中对比。

#### 4.1、Vue2中`updateChildren`会进行：

1. 头和头比
2. 尾和尾比
3. 头和尾比
4. 尾和头比
5. 都没有命中的对比



#### 4.2、Vue3中`patchKeyedChildren`会进行：

1. 头和头比
2. 尾和尾比
3. 基于最长递增子序列进行移动/添加/删除



#### 4.3举例说明

- 老的 children：`[ a, b, c, d, e, f, g ]`
- 新的 children：`[ a, b, f, c, d, e, h, g ]`

1. 先进行头和头比，发现不同就结束循环，得到 `[ a, b ]`
2. 再进行尾和尾比，发现不同就结束循环，得到 `[ g ]`
3. 再保存没有比较过的节点 `[ f, c, d, e, h ]`，并通过 newIndexToOldIndexMap 拿到在数组里对应的下标，生成数组 `[ 5, 2, 3, 4, -1 ]`，`-1` 是老数组里没有的就说明是新增
4. 然后再拿取出数组里的最长递增子序列，也就是 `[ 2, 3, 4 ]` 对应的节点 `[ c, d, e ]`
5. 然后只需要把其他剩余的节点，基于 `[ c, d, e ]` 的位置进行移动/新增/删除就可以了

使用最长递增子序列可以最大程度的减少 DOM 的移动，达到最少的 DOM 操作，有兴趣的话去 leet-code 第300题(最长递增子序列) 体验下。