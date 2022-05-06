# Class类

## 一、Class基本语法

### 1、class语法

```js
class myClass {
  //class方法
  constructor(){...};
  methods1(){};
  methods2(){};
  ...
}
```

然后使用 `new MyClass()` 来创建具有上述列出的所有方法的新对象。

`new` 会自动调用 `constructor()` 方法，因此<font color=red>可以在 `constructor()` 中初始化对象</font>。

```js
class User{
  constructor(name){
    this.name = name;
  }
  sayHi(){
    alert(this.name)
  }
}
//用法
let user = new User('zhang');
user.sayHi();
```

当`new User('zhang')`被调用时，会执行以下内容：

- 一个新对象被创建；
- `constructor`使用给定的参数运行，并为其分配this.name。

然后就可以调用对象方法了，比如上面的user.sayHi。

**注意⚠️：类的方法之间没有逗号**



### 2、什么是类

**在JS中，<font color=red>类是一种函数</font>。**

