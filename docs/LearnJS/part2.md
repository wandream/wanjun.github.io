<!--
 * @Author: Mia
 * @Date: 2022-01-06 17:33:29
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-10 09:48:26
 * @Description:
-->

## Promise 实现

[参考链接 - Promise 实现](https://juejin.cn/post/6844904096525189128#heading-3)

### 一、最简单的 Promise

```javascript
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("result");
  }, 1000);
});

p1.then(
  (res) => console.log(res),
  (err) => console.log(err)
);
```

### 二、低配版的 Promise

```javascript
class MyPromise {
  constructor(executor) {
    this._resolveQueue = []; // then 收集执行回调成功的队列
    this._rejectQueue = []; // then 收集执行回调失败的队列

    let _resolve = (val) => {
      // 从成功队列里取出回调并依次执行
      while (this._resolveQueue.length) {
        const callback = this._resolveQueue.shift();
        callback(val);
      }
    };

    // 实现 reject
    let _reject = (val) => {
      while (this._rejectQueue.length) {
        const callback = this._rejectQueue.shift();
        callback(val);
      }
    };
    // new Promise()时，立即执行executor
    executor(_resolve, _reject);
  }

  // then 方法，接收一个成功回调和一个失败回调，并push 进对应队列
  then(resolveFn, rejectFn) {
    this._resolveQueue.push(resolveFn);
    this._rejectQueue.push(rejectFn);
  }
}

const p2 = new MyPromise((resolve, reject) => {
  setTimeout(() => {
    resolve("myresult");
  }, 1000);
});

p2.then((res) => console.log(res));
```

### PromiseA+规范版

- 实现链式调用的关键点

1. .then() 需要返回一个 Promise，这样下一个才能拿调用.then()方法 => 把 then() 的返回值包装成 Promise
2. .then() 的回调需要拿到上一个 .then() 的返回值
3. .then() 的回调需要顺序执行，要等到当前的 Promise 状态变更后，再执行下一个 then 收集回调

- 值穿透 和 状态变更的情况

1. 值穿透：根据规范，当 then() 接收的参数不是 `Function` 时，那就应该忽略它；如果没有忽略，当 then() 进行回调的时候，不是 `Function` 会抛出异常，从而导致链式调用中断
2. 状态变更：then() 应该对应的状态是 pending。但有些时候，reject()/resolve()在 then()就已经被执行了 `Promise().resolve().then()`，如果这个时候，还把 then() 的回调 push 到队列中，回调将不会被执行。因此对应状态已经改变的情况就直接执行 then() 回调

- 兼容同步任务
  完成了 then 的链式调用以后，我们再处理一个前边的细节，然后放出完整代码。上文我们说过，Promise 的执行顺序是 new Promise -> then()收集回调 -> resolve/reject 执行回调，这一顺序是建立在 executor 是异步任务的前提上的，如果 executor 是一个同步任务，那么顺序就会变成 new Promise -> resolve/reject 执行回调 -> then()收集回调，resolve 的执行跑到 then 之前去了，为了兼容这种情况，我们给 resolve/reject 执行回调的操作包一个 setTimeout，让它异步执行

```javascript
// Promise A+
//Promise/A+规定的三种状态
const PENDING = "pending";
const FULFILLED = "fulfilled";
const REJECTED = "rejected";

class MyPromise {
  // 构造方法接收一个回调
  constructor(executor) {
    this._status = PENDING; // Promise状态
    this._value = undefined; // 储存then回调return的值，为了实现值穿透
    this._resolveQueue = []; // 成功队列, resolve时触发
    this._rejectQueue = []; // 失败队列, reject时触发

    // 由于resolve/reject是在executor内部被调用, 因此需要使用箭头函数固定this指向, 否则找不到this._resolveQueue
    let _resolve = (val) => {
      //把resolve执行回调的操作封装成一个函数,放进setTimeout里,以兼容executor是同步代码的情况
      const run = () => {
        if (this._status !== PENDING) return; // 对应规范中的"状态只能由pending到fulfilled或rejected"
        this._status = FULFILLED; // 变更状态
        this._value = val; // 储存当前value

        // 这里之所以使用一个队列来储存回调,是为了实现规范要求的 "then 方法可以被同一个 promise 调用多次"
        // 如果使用一个变量而非队列来储存回调,那么即使多次p1.then()也只会执行一次回调
        while (this._resolveQueue.length) {
          const callback = this._resolveQueue.shift();
          callback(val);
        }
      };
      setTimeout(run);
    };
    // 实现同reject
    let _reject = (val) => {
      const run = () => {
        if (this._status !== PENDING) return; // 对应规范中的"状态只能由pending到fulfilled或rejected"
        this._status = REJECTED; // 变更状态
        this._value = val; // 储存当前value
        while (this._rejectQueue.length) {
          const callback = this._rejectQueue.shift();
          callback(val);
        }
      };
      setTimeout(run);
    };
    // new Promise()时立即执行executor,并传入resolve和reject
    executor(_resolve, _reject);
  }

  // then方法,接收一个成功的回调和一个失败的回调
  then(resolveFn, rejectFn) {
    // 根据规范，如果then的参数不是function，则我们需要忽略它, 让链式调用继续往下执行
    typeof resolveFn !== "function" ? (resolveFn = (value) => value) : null;
    typeof rejectFn !== "function"
      ? (rejectFn = (reason) => {
          throw new Error(reason instanceof Error ? reason.message : reason);
        })
      : null;

    // return一个新的promise
    return new MyPromise((resolve, reject) => {
      // 把resolveFn重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论
      const fulfilledFn = (value) => {
        try {
          // 执行第一个(当前的)Promise的成功回调,并获取返回值
          let x = resolveFn(value);
          // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
          x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
        } catch (error) {
          reject(error);
        }
      };

      // reject同理
      const rejectedFn = (error) => {
        try {
          let x = rejectFn(error);
          x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
        } catch (error) {
          reject(error);
        }
      };

      switch (this._status) {
        // 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
        case PENDING:
          this._resolveQueue.push(fulfilledFn);
          this._rejectQueue.push(rejectedFn);
          break;
        // 当状态已经变为resolve/reject时,直接执行then回调
        case FULFILLED:
          fulfilledFn(this._value); // this._value是上一个then回调return的值(见完整版代码)
          break;
        case REJECTED:
          rejectedFn(this._value);
          break;
      }
    });
  }
}

const p1 = new MyPromise((resolve, reject) => {
  resolve(1); //同步executor测试
});

p1.then((res) => {
  console.log(res);
  return 2; //链式调用测试
})
  .then() //值穿透测试
  .then((res) => {
    console.log(res);
    return new MyPromise((resolve, reject) => {
      resolve(3); //返回Promise测试
    });
  })
  .then((res) => {
    console.log(res);
    throw new Error("reject测试"); //reject测试
  })
  .then(
    () => {},
    (err) => {
      console.log(err);
    }
  );

// 输出
// 1
// 2
// 3
// Error: reject测试
```


### Promise.prototype.catch
> `catch() 方法` 返回一个 Promise，并且处理拒绝的情况。它的行为同调用 Promise.prototype.then(undefind, onRejected)是相同的
```javascript
catch(rejectFn) {
  return this.then(undefined, rejectFn)
}
```

### Promise.prototype.finally
> `finally() 方法` 返回一个 Promise。在 Promise 结束的时候，无论结果是 fulfilled 或者是 rejected，都会执行指定的回调函数。这为在 Promise 是否成功完成后都需要执行提供了一种方式
```javascript
p.finally(onFinally);

p.finally(function() {
  // 返回状态为(resolved 或 rejected)
})
```

### Promise.all
> `Promise.all(iterable)`方法返回一个 Promise 实例，此实例在 iterable 参数内所有的 promise 都 resolved 或参数中不包含 promise 时回调完成；如果参数中 promise 有一个失败，此实例回调失败，失败的原因是第一个失败 promise 的结果

```javascript
// 静态的 all 方法
static all(promiseArr) {
  let index = 0
  let result = []
  return new MyPromise((resolve, reject) => {
    promiseArr.forEach((item, ind) => {
      MyPromise.resolve(item).then(val => {
        index++
        result[i] = val
        // 所有 then 执行之后，resolve结果
        if(index === promiseArr.length) {
          resolve(result)
        }
      }, err => {
        // 有一个 Promise 被 reject 时，MyPromise 的状态变为 reject
        reject(err)
      })
    })
  })
}
```