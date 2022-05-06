# 一、Object.defineProperty()

### 1、作用：

​	在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回这个对象。



### 2、语法

```js
Object.definePoperty(obj, prop, descriptor)
```

参数依次为：要添加属性的对象；要定义或修改的属性的名称或symbol；要定义或修改的属性描述符；



```js
//举个例子
let person = {}
let personName = 'lihua'

//在person对象上添加属性namep,值为personName
Object.defineProperty(person, 'namep', {
    //但是默认是不可枚举的(for in打印打印不出来)，可：enumerable: true
    //默认不可以修改，可：wirtable：true
    //默认不可以删除，可：configurable：true
    get: function () {
        console.log('触发了get方法')
        return personName
    },
    set: function (val) {
        console.log('触发了set方法')
        personName = val
    }
})

//当读取person对象的namp属性时，触发get方法
console.log(person.namep)

//当修改personName时，重新访问person.namep发现修改成功
personName = 'liming'
console.log(person.namep)

// 对person.namep进行修改，触发set方法
person.namep = 'huahua'
console.log(person.namep)
```

以上方法监听了person上的name属性的变化。



### 3、监听多个属性

配合Object.keys(obj)进行遍历。这个方法可以返回obj对象身上的所有可枚举属性组成的字符数组。（其实用for in遍历也可以。

```js
//实现方式
let person = {
    name: '',
    age: 0
}
// 实现一个响应式函数
function defineProperty(obj, key, val) {
    Object.defineProperty(obj, key, {
        get() {
            console.log(`访问了${key}属性`)
            return val
        },
        set(newVal) {
            console.log(`${key}属性被修改为${newVal}了`)
            val = newVal
        }
    })
}
// 实现一个遍历函数Observer
function Observer(obj) {
    Object.keys(obj).forEach((key) => {
        defineProperty(obj, key, obj[key])
    })
}
Observer(person)
console.log(person.age)
person.age = 18
console.log(person.age)
```



### 4、深度监听一个对象

监听对象中嵌套对象的情况，需要使用递归的思想。

- 在defineProperty()函数中，添加一个递归的情况：

```js
function defineProperty(obj, key, val) {
    //如果某对象的属性也是一个对象，递归进入该对象，进行监听
    if(typeof val === 'object'){
    observer(val)
    }
    Object.defineProperty(obj, key, {
        get() {
            console.log(`访问了${key}属性`)
            return val
        },
        set(newVal) {
            console.log(`${key}属性被修改为${newVal}了`)
            val = newVal
        }
    })
}
```

- 在Obsever里添加一个递归停止条件

```js
function Observer(obj) {
    //如果传入的不是一个对象，return
    if (typeof obj !== "object" || obj === null) {
        return
    }
    // for (key in obj) {
    Object.keys(obj).forEach((key) => {
        defineProperty(obj, key, obj[key])
    })
    // }
}
```

- 如果修改属性时，原本属性值是个字符串，但是我们重新赋值了一个对象，想要监听新添加的对象的属性的方法：

```js
set(newVal) {
  // 如果newVal是一个对象，递归进入该对象进行监听
  if(typeof val === 'object'){
    observer(key)
  }
  console.log(`${key}属性被修改为${newVal}了`)
  val = newVal
}
```



# 二、Proxy

结合上面的Object.defineProperty方法，当我要给对象新增加一个属性时，也需要手动去监听这个新增属性。也正是因为这个原因，使用vue给 data 中的数组或对象新增属性时，需要使用 vm.$set 才能保证新增的属性也是响应式的。



### 1、语法

```js
const p = new Proxy(target, handler)
```

参数的意义：

​	**target**：要使用proxy包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）

​	**handler**：一个通常以函数作为属性的对象，各属性中的函数分别定义了在执行各种操作时代理proxy的行为。

通过Proxy，我们可以对<font color=orange>设置代理的对象</font>上的一些操作进行拦截，外界对这个对象的各种操作，都要先通过这层拦截。（和defineProperty差不多）



```js
//举个例子
let person = {
    age: 0,
    school: '西电'
}
//定义handler对象
let hander = {
    get(obj, key) {
        // 如果对象里有这个属性，就返回属性值，如果没有，就返回默认值66
        return key in obj ? obj[key] : 66
    },
    set(obj, key, val) {
        obj[key] = val
        return true
    }
}
//把handler对象传入Proxy
let proxyObj = new Proxy(person, hander)

// 测试get能否拦截成功
console.log(proxyObj.age)//输出0
console.log(proxyObj.school)//输出西电
console.log(proxyObj.name)//输出默认值66

// 测试set能否拦截成功
proxyObj.age = 18
console.log(proxyObj.age)//输出18 修改成功
```

Proxy代理的是整个对象，而不是对象的某个特定属性，不需要遍历来逐个进行数据绑定。

<font color=red>注意⚠️</font>：之前我们在使用Object.defineProperty()给对象添加一个属性之后，我们对对象属性的读写操作仍然<font color=orange>在对象本身</font>。但是一旦使用Proxy，如果想要读写操作生效，<font color=orange>就要对Proxy的实例对象`proxyObj`进行操作</font>。

另外，set()方法**应该返回一个布尔值**，否则会报类型错误。



### 2、解决Object.defineProperty中的问题

- 一次只能对一个属性进行监听，需要遍历来对所有属性监听。这个我们在上面已经解决了；
- 在遇到一个对象的属性还是一个对象的情况下，需要递归监听；
- 对于对象的新增属性，需要手动监听；
- 对于数组通过push、unshift方法增加的元素，也无法监听

解决问题二

```js
let person = {
    age: 0,
    school: '西电',
    children: {
        name: '小明'
    }
}
let hander = {
    get(obj, key) {
        return key in obj ? obj[key] : 66
    }, set(obj, key, val) {
        obj[key] = val
        return true
    }
}
let proxyObj = new Proxy(person, hander)

// 测试get
console.log(proxyObj.children.name)//输出：小明
console.log(proxyObj.children.height)//输出：undefined
// 测试set
proxyObj.children.name = '菜菜'
console.log(proxyObj.children.name)//输出: 菜菜
```

解决问题三

这个其实在基本使用里面已经提到了，访问的proxyObj.name就是原本对象上不存在的属性，但是我们访问它的时候，仍然们可以被get拦截到。

解决问题四

```js
let subject = ['高数']
let handler = {
    get(obj, key) {
        return key in obj ? obj[key] : '没有这门学科'
    }, set(obj, key, val) {
        obj[key] = val
        //set方法成功时应该返回true，否则会报错
        return true
    }
}

let proxyObj = new Proxy(subject, handler)

// 检验get和set
console.log(proxyObj)//输出  [ '高数' ]
console.log(proxyObj[1])//输出  没有这门学科
proxyObj[0] = '大学物理'
console.log(proxyObj)//输出  [ '大学物理' ]

// // 检验push增加的元素能否被监听
proxyObj.push('线性代数')
console.log(proxyObj)//输出 [ '大学物理', '线性代数' ]
```



### 3、Proxy还支持13种拦截操作

除了get和set来拦截读取和赋值操作之外，Proxy还支持对其他多种行为的拦截。



### 4、Proxy中有关this的问题

虽然Proxy完成了对目标对象的代理，但是它不是透明代理，也就是说：即使handler为空对象（即不做任何代理），他所代理的对象中的this指向也不是该对象，而是proxyObj对象。

```js
let target = {
    m() {
        // 检查this的指向是不是proxyObkj
        console.log(this === proxyObj)
    }
}
let handler = {}
let proxyObj = new Proxy(target, handler)

proxyObj.m()//输出:true
target.m()//输出:false
```



除此之外，有的js内置对象的内部属性，也依靠正确的this才能获取，所以Proxy 也无法代理这些原生对象的属性。

```js
const target = new Date();
const handler = {};
const proxyObj = new Proxy(target, handler);

proxyObj.getDate();
// TypeError: this is not a Date object.
```

