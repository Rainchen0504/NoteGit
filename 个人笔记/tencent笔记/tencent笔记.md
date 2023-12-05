# 2023年12月5日

## 1、JS对象属性顺序

ECMAScript2020开始，`Object.key`、`for...in`、`Object.getOwnPropertyNames`、`Reflect.ownKeys`都遵循以下规则：

### （1）key为自然数

即key为0或正整数，按照数字大小进行**<font color=deepred>升序排列</font>**

```js
const obj = {
  8: 8,
  1: 1,
  4: 4
}
console.log(Object.keys(obj)) 
//[ '1', '4', '8' ]
```

### （2）key为字符串

按照加入的时间顺序进行排列

```js
const obj = {
    "-1" : -1,
    c :'c',
    a : "a",
    "001": "001"
}
console.log(Object.keys(obj))
// [ '-1', 'c', 'a', '001' ]
```

### （3）key为混合类型

先按照自然数升序排列，然后按照字符串的加入时间排序

```js
const obj = {
  c: 8,
  1: 1,
  b: 4,
};
console.log(Object.keys(obj));
// [ '1', 'c', 'b' ]
```

