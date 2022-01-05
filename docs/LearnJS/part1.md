<!--
 * @Author: Mia
 * @Date: 2022-01-05 09:23:53
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-05 10:31:44
 * @Description:
-->

## 构造函数的基本结构

```javascript
var Keith = function () {
  this.height = 100;
};
function Keith() {
  this.height = 100;
}
```

## 构造函数的作用

构造函数主要用来在创建对象时完成对对象属性的一些初始化操作，当创建对象时，对象会自动调用它的构造函数。一般来说，构造函数有以下三个方面的作用：

- 给创建的对象建立一个标识符
- 为对象数据成员开辟内存空间
- 完成对象数据成员的初始化

## 默认构造函数

当用户没有显式的去定义构造函数时，编译器会为类生成一个默认的构造函数，称为`默认构造函数`，默认构造函数不能完成对象数据成员的初始化，只能给对象创建一标识符，并为对象中的数据成员开辟一定的内存空间。

## 构造函数的三大特点:

1. 构造函数的函数名的第一个字母通常大写
2. 函数体内使用 `this` 关键字，代表所要生成的对象实例
3. 生成对象的时候，必须使用 `new` 命令来调用构造函数

```javascript
function create() {
  const obj = new Object(); // 创建一个空对象
  constructor = [].shift.call(arguments); // 获取构造函数，arguments中的第一个参数
  obj.__proto__ = constructor.prototype;
  var ret = constructor.apply(obj, arguments);
  return ret instanceof Object ? ret : obj;
}
```

## js 中的类

在面向对象编程中，类（clas）是对象（object）的模板，定义了同一组对象共有的属性和方法。js 本身不支持类，但可以用一些变通的方法来模拟。
- 构造函数
```JavaScript
function Cat() {
  this.name = 'tom',
}
const cat = new Cat()
```
- Object.create()
这种方法有一定的兼容性问题，不能实现私有属性和私有方法，实例对象之间也不能共享数据
```javascript
const Cat = {
  name: "tom",
  makeSound: funtion() {
    console.log('喵喵喵')
  }
}
const cat = object.create(Cat)
```
- 极简主义
```JavaScript
const Person = {
  createNew: function() {
    var Person = {};
    Person.type = '人';
    Person.run = function() {
      console.log('run')
    }
    return Person
  }
}
var person =  Person.createNew()
```

