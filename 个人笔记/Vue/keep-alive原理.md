```vue
<keep-alive></keep-alive>
```

该组件是Vue中内置的一个抽象组件，自身不会渲染一个DOM元素，也不会出现在父组件链中。它包裹动态组件时，会缓存不活动的组件实例，而不是销毁。



### 属性

`<keep-alive></keep-alive>`组件接收三个属性：

- `include`字符串或正则表达式，只有名称匹配的组件会被缓存；
- `exclude`字符串或正则表达式，任何名称匹配的组件都不会被缓存；
- `max`最多可以缓存多少组件实例；



### 原理

```js
// 源码
export default {
  name: 'keep-alive',
  abstract: true,
 
  props: {
    include: [String, RegExp, Array],
    exclude: [String, RegExp, Array],
    max: [String, Number]
  },
 
  created () {
    this.cache = Object.create(null)
    this.keys = []
  },
 
  destroyed () {
    for (const key in this.cache) {
      pruneCacheEntry(this.cache, key, this.keys)
    }
  },
 
  mounted () {
    this.$watch('include', val => {
      pruneCache(this, name => matches(val, name))
    })
    this.$watch('exclude', val => {
      pruneCache(this, name => !matches(val, name))
    })
  },
 
  render() {
    /* 获取默认插槽中的第一个组件节点 */
    const slot = this.$slots.default
    const vnode = getFirstComponentChild(slot)
    /* 获取该组件节点的componentOptions */
    const componentOptions = vnode && vnode.componentOptions
 
    if (componentOptions) {
      /* 获取该组件节点的名称，优先获取组件的name字段，如果name不存在则获取组件的tag */
      const name = getComponentName(componentOptions)
 
      const { include, exclude } = this
      /* 如果name不在inlcude中或者存在于exlude中则表示不缓存，直接返回vnode */
      if (
        (include && (!name || !matches(include, name))) ||
        // excluded
        (exclude && name && matches(exclude, name))
      ) {
        return vnode
      }
 
      const { cache, keys } = this
      const key = vnode.key == null
        // same constructor may get registered as different local components
        // so cid alone is not enough (#3269)
        ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
        : vnode.key
      if (cache[key]) {
        vnode.componentInstance = cache[key].componentInstance
        // make current key freshest
        remove(keys, key)
        keys.push(key)
      } else {
        cache[key] = vnode
        keys.push(key)
        // prune oldest entry
        if (this.max && keys.length > parseInt(this.max)) {
          pruneCacheEntry(cache, keys[0], keys, this._vnode)
        }
      }
 
      vnode.data.keepAlive = true
    }
    return vnode || (slot && slot[0])
  }
}
```

该组件有3个属性，即有3个props，同时包含4个生命周期钩子函数：`created、destoryed、mounted、render`。



### created和destoryed

created钩子会创建一个cache对象，用来作为缓存容器、保存vnode节点；

destoryed钩子在组件被销毁的时候清除cache缓存中的所有组件实例；

```js
 created () {
    this.cache = Object.create(null)
    this.keys = []
  },
 
  destroyed () {
    for (const key in this.cache) {
      pruneCacheEntry(this.cache, key, this.keys)
    }
  },
```



### render

keep-alive实现缓存的核心代码都在此钩子函数中，运用了**LRU缓存淘汰策略**

1. 先获取插槽中的内容;

2. 调用`getFirstComponentChild`方法获取第一个子组件，获取到该组件的`name`，如果有name属性就用name，没有就用tag；

   ```js
   /* 获取该组件节点的名称 */
   const name = getComponentName(componentOptions)
    
   /* 优先获取组件的name字段，如果name不存在则获取组件的tag */
   function getComponentName (opts: ?VNodeComponentOptions): ?string {
     return opts && (opts.Ctor.options.name || opts.tag)
   }
   ```

3. 用获取到的`name`和传入的`include、exclude`属性进行匹配，如果匹配不成功，则表示不缓存该组件，直接返回这个组件的vnode节点，否则进入下一步缓存；

   ```js
   const { include, exclude } = this
   /* 如果name与include规则不匹配或者与exclude规则匹配则表示不缓存，直接返回vnode */
   if (
       (include && (!name || !matches(include, name))) ||
       // excluded
       (exclude && name && matches(exclude, name))
   ) {
       return vnode
   }
   ```

4. 缓存机制，用拿到的name属性去`this.cache对象中查找是否存在该值，如果有表示有缓存，即缓存命中`；

   ```js
   /* 如果🎯命中缓存，则直接从缓存中拿vnode的组件实例 */
   if ( cache[key] ) {
     vnode.commponentInstance = cache[key].componentInstance
     // 调整该组件的key的顺序，将其从原来的地方删除并重新放在最后一个
     remove( keys, key )
     keys.push( key )
   } else {
   /* 如果没命中缓存，则将其设置进缓存 */
     cache[key] = vnode
     keys.push(key)
     /* 如果配置了max并且缓存的长度超过了this.max，则从缓存中删除第一个 */
     if (this.max && keys.length > parseInt(this.max)) {
       pruneCacheEntry(cache, keys[0], keys, this._vnode)
     }
   }
   /* 最后设置keepAlive标记位 */
   vnode.data.keepAlive = true
   ```

   - 命中缓存时会直接从缓存中获取vnode组件实例，此时重新调整该组件key的顺序，将其从原来的地方删掉并重新放在`this.keys`中最后一个。

   - 如果没有命中缓存即组件没被缓存过，则以该组件的key为键，组件vnode为值，存入`this.cache`中，并把key存入`this.keys`中，同时判断`this.keys`中缓存组件的数量是否超过了设置的最大存储数量`this.max`，如果超过了就把第一个缓存的组件删除掉。



### mounted

该钩子调用`pruneCache`方法观察`include`和`exclude`的变化。

```js
function pruneCache (keepAliveInstance, filter) {
  const { cache, keys, _vnode } = keepAliveInstance
  for( const key in cache ) {
    const cachedNode = cache[key]
    if ( cachedNode ) {
      const name = getComponentName( cachedNode.componentOptions )
      if (name && !filter(name)) {
        pruneCacheEntry(cache, key, keys, _vnode)
      }
    }
  }
}
```

在该函数内对`this.cache`对象进行遍历，取出每一项的name值，用其与新的缓存规则进行匹配，如果匹配不上，则表示在新的缓存规则下该组件已经不需要被缓存，则调用`pruneCacheEntry`函数将其从`this.cache`对象剔除即可。



### 总结

​	组件一旦被 `<keep-alive>` 缓存，那么再次渲染的时候就不会执行 created、mounted 等钩子函数。使用keepalive组件后，被缓存的组件生命周期会多activated和deactivated 两个钩子函数，它们的执行时机分别是 `<keep-alive>` 包裹的组件激活时调用和停用时调用。