<!--
 * @Author: Mia
 * @Date: 2022-01-12 09:22:20
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-12 10:27:35
 * @Description: typeof 和 instanceof 的区别
-->

## Typeof 和 instanceof 区别

[参考链接](https://github.com/febobo/web-interview/issues/65)

`typeof`操作符返回一个字符串，表示未经计算的操作数的类型
```javascript
// javascript 存在一个悠久的bug
typeof null // object
// 这并不代表null就是引用类型数据，并且null 本身也不是对象
```

`instanceof`运算符用于检测构造函数的prototype属性是否出现在某个实例对象的原型链上


##### 区别
`typeof`与`instanceof`都是判断数据类型的方法，区别如下：
 - typeof 会返回一个变量的基本类型，instanceof 返回的是一个布尔值
 - instanceof 可以准确地判断复杂引用数据类型，但不能正确判断基础数据类型
 - 而 typeof 也存在弊端，它虽然可以判断基础数据类型(null 除外)，但是引用数据类型中，除了 function 类型外，其他的无法判断

 ```javascript
function getType(obj) {
  let type = typeof obj
  if(type !== "object") {
    return type
  }
  return Object.prototype.toString.call(obj).replace(/^\[object (\S+)\]$/, '$1')
}
 ```