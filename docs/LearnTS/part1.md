<!--
<!--
 * @Author: Mia
 * @Date: 2021-12-28 10:19:44
 * @LastEditors: Mia
 * @LastEditTime: 2021-12-29 15:24:09
 * @Description: 
-->
## TS

type 和 interface 的区别
1. 两者都可以用来描述对象或函数的类型，但是语法不同

```javascript
interface Point {
  x: number,
  y: number
}
interface setPoint {
  (x: number, y:number): void
}
```

```javascript
type Point = {
  x: number,
  y: number
}
type setPoint = (x: number, y: number) => void
```

2. 与接口不同，类型别名还可以用于其他类型
```javascript
// 定义string的别名
type Name = string
// 定义object
type partialPointX = { x: string }
type partialPointY = { y: number }
// 定义联合类型
type partialPoint = partialPointX | partialPointY
// 定义元组
type Data = [number, string]
```

3. 扩展
type 和 interface 都可以扩展，但是语法有所不同。此外，需要注意的是：接口和类型别名不是互斥的。接口可以扩展类型别名，反之亦然
- interface extends interface
```javascript
interface partialPointX { x: number }
interface point extends partialPointX { y: number }
```
- type alias extends type alias
```javascript
type partialPointX = { x: number }
type point = partialPoinX & { y: number }
```
- interface extends type alias
```javascript
type partialPointX = { x: number }
interface point extends partialPointX { y: number }
```
- type alias extends interface
```javascript
interface partialPointX { x: number }
type point = partialPointX & { y: number }
```

4. 与类型别名不同，接口可以定义多次，并被视为单接口（合并所有声明的成员）
```javascript
interface point { x: number }
interface point { y: number }
const pointDetail: point = { x: 1, y: 2}
```


