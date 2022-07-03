# 一、数组

### 1、求斐波那契数列的前 20 个数

```javascript
const fibonacci = [];
fibonacci[1] = 1;
fibonacci[2] = 1;
for (let i = 3; i < 20; i++) {
  fibonacci[i] = fibonacci[i - 1] + fibonacci[i - 2];
}
console.log(...fibonacci);
```

