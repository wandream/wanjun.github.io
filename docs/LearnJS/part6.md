call、apply、bind

[参考链接 - 掘金](https://juejin.cn/post/6844904153437700103)

一、call

第一个参数可以改变调用函数的 this 指向， 第二个以及之后的参数为传入的函数的参数

```javascript
let obj = {
  a: 1
}
function fn(name, age) {
  console.log(this.a)
  console.log(name)
  console.log(age)
}
fn.call(obj, 'lihua', 12)
// 1
// lihua
// 12
```
**怎么改变this指向**
根据this特性 对象的方法调用 那么方法内部的 this 就指向这个对象
```javascript
let obj = {
  a: 1,
  fn(name, age) {
    console.log(this.a)
    console.log(name)
    console.log(age)
  }
}
obj.fn('lihua', 12)
```
**怎么获取传入的不定参数**<br />
<font color=red>利用es6 ...args剩余参数获取方法(rest)</font>

### call 的具体实现
```javascript
Function.prototype.myCall = function(context, ...args) {
  if(!context || context === null) {
    context = window;
  }
  // 创造唯一的key值，作为我们构造的 context 内部方法名
  let fn = Symbol()
  context[fn] = this // this 指向调用call的函数
  // 执行函数并返回结果，相当于自身作为传入的 context 的方法进行调用
  return context[fn](...args)
}
```
### apply的实现
apply原理和call实现一致，只是第二个参数传入的是数组
```javascript
Function.prototype.myApply = function(context, args) {
  if(!context || context === null) {
    context = window
  }
  // 创造唯一的 key值，作为我们构造的 context 内部方法名
  let fn = Symbol();
  context[fn] = this
  // 执行函数并返回结果
  return context[fn](...args)
} 
//测试一下 call 和 apply
let obj = {
  a: 1
};
function fn(name, age) {
  console.log(this.a);
  console.log(name);
  console.log(age);
}
fn.myCall(obj, "我是lihua", "18");
fn.myApply(obj, ["我是lihua", "18"]);
let newFn = fn.myBind(obj, "我是lihua", "18");
newFn();
```
### bind
bind实现要复杂一点，因为他考虑的情况比较多，还要涉及到参数合并（类似函数柯里化）
```JavaScript
Function.prototype.myBind = function (context, ...args) {
  if (!context || context === null) {
    context = window;
  }
  // 创造唯一的key值  作为我们构造的context内部方法名
  let fn = Symbol();
  context[fn] = this;
  let _this = this
  //  bind情况要复杂一点
  const result = function (...innerArgs) {
    // 第一种情况 :若是将 bind 绑定之后的函数当作构造函数，通过 new 操作符使用，则不绑定传入的 this，而是将 this 指向实例化出来的对象
    // 此时由于new操作符作用  this指向result实例对象  而result又继承自传入的_this 根据原型链知识可得出以下结论
    // this.__proto__ === result.prototype   //this instanceof result =>true
    // this.__proto__.__proto__ === result.prototype.__proto__ === _this.prototype; //this instanceof _this =>true
    if (this instanceof _this === true) {
      // 此时this指向指向result的实例  这时候不需要改变this指向
      this[fn] = _this
      this[fn](...[...args, ...innerArgs]) //这里使用es6的方法让bind支持参数合并
      delete this[fn]
    } else {
      // 如果只是作为普通函数调用  那就很简单了 直接改变this指向为传入的context
      context[fn](...[...args, ...innerArgs]);
      delete context[fn]
    }
  };
  // 如果绑定的是构造函数 那么需要继承构造函数原型属性和方法
  // 实现继承的方式一:  构造一个中间函数来实现继承
  // let noFun = function () { }
  // noFun.prototype = this.prototype
  // result.prototype = new noFun()
  // 实现继承的方式二: 使用Object.create
  result.prototype = Object.create(this.prototype)
  return result
};
//测试一下

function Person(name, age) {
  console.log(name); //'我是参数传进来的name'
  console.log(age); //'我是参数传进来的age'
  console.log(this); //构造函数this指向实例对象
}
// 构造函数原型的方法
Person.prototype.say = function() {
  console.log(123);
}
let obj = {
  objName: '我是obj传进来的name',
  objAge: '我是obj传进来的age'
}
// 普通函数
function normalFun(name, age) {
  console.log(name);   //'我是参数传进来的name'
  console.log(age);   //'我是参数传进来的age'
  console.log(this); //普通函数this指向绑定bind的第一个参数 也就是例子中的obj
  console.log(this.objName); //'我是obj传进来的name'
  console.log(this.objAge); //'我是obj传进来的age'
}

// 先测试作为构造函数调用
// let bindFun = Person.myBind(obj, '我是参数传进来的name')
// let a = new bindFun('我是参数传进来的age')
// a.say() //123

// 再测试作为普通函数调用
let bindFun = normalFun.myBind(obj, '我是参数传进来的name')
 bindFun('我是参数传进来的age')
```