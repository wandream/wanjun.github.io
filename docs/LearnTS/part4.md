<!--
 * @Author: Mia
 * @Date: 2022-01-06 17:07:30
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-11 16:42:13
 * @Description: 
-->
### TS React的一些实践

#### 函数类型

```typescript
type FunctionTypes = {
  //bad 任何可调用的函数
  onSomething: Function; 

  // better
  onClick: () => void // 明确无参数无返回值的函数
  onChange:(id:number) => void // 明确参数无返回值的函数
  onClick:(event:React.MouseEvent<HTMLButtonElement>):void
}
```

#### Promise 类型
``` typescript
// 定义一个类型
type IResponse<T> = {
  message: string
  result: T
  success: boolean
}
// 定义一个方法
async function getResponse():Promise<IResponse<number[]>> {
  return {
    message: '获取成功',
    result:[1,2,3],
    success: true
  }
}
// promise调用
getResponse().then(res => {
  console.log(res)
})
```
typescript 中定义 promise 的返回值类型
```typescript
// 此时main的类型为 Promise<{}>
const main = new Promise((resolve, reject) => {
  resolve(1)
})
// 解决方法一：通过Promsie的构造函数，声明返回值的泛型类型
const main = new Promise<number>((resolve, reject) => {
  resolve(1)
})
// 解决方法二：修改
```


#### 类型保护
> 类型保护允许使用更下范围下的对象类型

 - typeof
 ```typescript
function doSome(x:number | string) {
  if(typeof x === 'string') {
    // 在这个块中，ts 知道 'x' 的类型必须是 string
    console.log(x.subtr(1))// Error sbutr 方法不存在于 string 上
    console.log(x.substr(1))
  } 
  x.substr(1); // Error: 无法保证 `x` 是 `string` 类型
}
 ```

 - instanceof
 ```typescript
class Foo {
  foo = 123,
  common = '123123',
}

class Bar {
  bar = 123,
  common = '123'
}

function doStuff(arg: Foo | Bar) {
  if(arg instanceof Foo) {
    console.log(arg.foo) // ok
    console.log(arg.bar) // Error
  }
  if(arg instanceof Bar) {
    console.log(arg.foo)
    console.log(arg.bar)
  }
}
doStuff(new Foo());
doStuff(new Bar());
 ```

 - in
```typescript
interface A {
  x: number
}

interface B {
  y: string
}

function doStuff(q: A | B) {
  if('x' in q) {
    // q:A
  } else {
    // q:B
  }
}
```

 - is

 ```typescript
function isString(test:any):test is string {
  return typeof test === 'string'
}
function example(foo: number | string) {
  if(isString(foo)) {
    console.log('it is a string'+foo)
    console.log(foo.length)
  }
}

example('hello world')
// is 为关键字的 [类型谓语]
// 把参数的类型范围缩小了
// 当私用 test is string 之后，我们通过 isString(foo) === true
// 明确知道其中的参数是 string
// 而 boolean 并没有这个能力
// 这就是 is 关键字存在的意义
 ```

#### 索引、映射、条件、断言类型
```typescript
// 用 extends 关键字判断两个类型的子类型关系
type isSubTyping<Child, Par> = Child extends Par ? true : false
```