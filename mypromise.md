# 学习记录 8-22

### 自定义 Promise 的实现

1. 初始化构造函数 MyPromise 的属性和方法
2. 在 MyPromise 的原型对象上新增一个 then 方法，该方法接受两个参数，分别是成功的回调函数和失败的回调函数
3. 完善 then 方法，then 方法返回一个新的 MyPromise 实例对象，该对象的返回值是根据旧的实例对象（即调用 then 方法的实例对象）的状态来决定回调方法的使用。当 MyPromise 执行一个异步任务，调用 then 方法时，此时的状态为 pending ，需要先保存传入的两个回调方法到实例对象的 callbacks 属性中，待异步任务执行结束后，根据状态调用相对应的回调。
4. 在 MyPromise 的原型对象上新增一个 catch 方法，该方法接受一个参数，是一个失败和抛出错误的回调函数。
5. 实现 then 方法的链式调用，当 then 方法传入的回调函数参数为空或者只有一个回调函数时，为保证上一个实例对象的结果值向下传递，需要先对传入的回调的参数进行校验，若不是一个 function，则需要手动添加一个默认回调函数。
6. resolve 方法的实现。resolve 方法需要根据传入的参数来决定新的实例对象的状态值并返回该对象，当传入的参数是一个 myPromise 实例对象时，返回一个与该对象一摸一样的实例（状态值和结果值都相同）；当传入的参数为 undefined、number、string 等类型时，返回一个成功状态的实例对象，结果值为传入的参数。
7. reject 方法的实现。根据传入的参数返回一个新的 MyPromise 实例对象，且状态值为 rejected 。
8. race 方法的实现。传入的参数为一个 myPromise 实例对象的数组，遍历该数组然后返回一个新的实例对象，该实例对象与第一个修改状态的实例相同。
9. all 方法的实现。传入的参数为一个 myPromise 实例对象的数组，遍历该数组然后返回一个新的实例对象，用一个属性记录状态值为成功的数量，另一个属性为状态值为成功的结果值的数组，若全部为成功，则返回一个新的 fulfilled 状态的实例对象，结果值为一个数组，否则返回一个 rejected 状态的新的实例对象。

```js
function MyPromise(executor) {
  //MyPromise 的状态
  this.PromiseState = "pending";
  //MyPromise 的结果值
  this.PromiseResult = null;
  //保存当存在多个 then （不是链式调用）和异步任务的回调方法
  this.callbacks = [];

  const resolve = (data) => {
    //保证 MyPromise 第一次修改状态值时才调用
    if (this.PromiseState !== "pending") return;
    this.PromiseState = "fulfilled";
    this.PromiseResult = data;
    //当 MyPromise的 任务为异步任务时，保证异步任务执行结束后执行相对应的回调方法
    this.callbacks.forEach((item) => item.onResolved(data));
  };

  const reject = (data) => {
    //保证 MyPromise 第一次修改状态值时才调用
    if (this.PromiseState !== "pending") return;
    this.PromiseState = "rejected";
    this.PromiseResult = data;
    //当 MyPromise的 任务为异步任务时，保证异步任务执行结束后执行相对应的回调方法
    this.callbacks.forEach((item) => item.onRejected(data));
  };

  try {
    executor(resolve, reject);
  } catch (error) {
    reject(error.message ? error.message : error);
  }
}

MyPromise.prototype.then = function (onResolved, onRejected) {
  //保存 this 的指向，保证下面函数调用 self 始终指向 MyPromise 的实例对象
  const self = this;

  //保证 then 的链式调用
  if (typeof onResolved !== "function") {
    onResolved = (res) => res;
  }

  //保证 then 的链式调用
  if (typeof onRejected !== "function") {
    onRejected = (err) => {
      throw new Error(err);
    };
  }
  return new MyPromise((resolve, reject) => {
    //提取公共逻辑，根据传入的回调函数，修改 Promise 实例对象的状态
    function promiseCallback(stateCallback) {
      try {
        const result = stateCallback(self.PromiseResult);
        if (result instanceof MyPromise) {
          //当 result 是 MyPromise 的实例对象时，根据 result 的状态来决定返回的 MyPromise 的状态
          result.then(
            (res) => resolve(res),
            (err) => reject(err)
          );
        } else {
          //当 result 为 undefined 时
          resolve(result);
        }
      } catch (error) {
        reject(error.message ? error.message : error);
      }
    }

    //当 MyPromise 实例对象的状态为 fulfilled 时，
    if (self.PromiseState === "fulfilled") {
      //使用定时器模拟异步执行回调，在原生的Promise中，回调方法是异步执行的
      setTimeout(() => promiseCallback(onResolved));
    }
    if (self.PromiseState === "rejected") {
      //使用定时器模拟异步执行回调，在原生的Promise中，回调方法是异步执行的
      setTimeout(() => promiseCallback(onRejected));
    }
    if (self.PromiseState === "pending") {
      //当Promise 的任务为一个异步任务时，将 then 方法的回调保存在实例对象的 callbacks 属性上
      self.callbacks.push({
        onResolved: () => {
          //使用定时器模拟异步执行回调，在原生的Promise中，回调方法是异步执行的
          setTimeout(() => promiseCallback(onResolved));
        },
        onRejected: () => {
          //使用定时器模拟异步执行回调，在原生的Promise中，回调方法是异步执行的
          setTimeout(() => promiseCallback(onRejected));
        },
      });
    }
  });
};

MyPromise.prototype.catch = function (onRejected) {
  return this.then(undefined, onRejected);
};

MyPromise.resolve = function (value) {
  //返回一个新的 MyPromise 实例对象
  return new MyPromise((resolve, reject) => {
    //当传入的 value 是 MyPromise 的实例对象时，根据传入的 value 返回相同的状态值和结果值
    if (value instanceof MyPromise) {
      value.then(
        (res) => resolve(res),
        (err) => reject(err)
      );
    } else {
      resolve(value);
    }
  });
};

MyPromise.reject = function (value) {
  //返回一个新的 MyPromise 实例对象，且该对象的状态值一定为 rejected
  return new MyPromise((resolve, reject) => {
    reject(value);
  });
};

MyPromise.race = function (promiseList) {
  //返回一个新的 MyPromise 实例对象,该对象与首个改变状态的 MyPromise 实例对象相同
  return new MyPromise((resolve, reject) => {
    //遍历 MyPromise 实例对象数组，根据第一个改变状态的 MyPromise 修改状态值
    promiseList.forEach((item) => {
      item.then(
        //虽然循环调用resolve和reject，但只改变一次
        (res) => resolve(res),
        (err) => reject(err)
      );
    });
  });
};

MyPromise.all = function (promiseList) {
  //返回一个新的 MyPromise 实例对象，当数组的所有的 MyPromise 对象实例都是成功状态时，新的实例对象的结果值是一个包含数组中所有实例对象的结果值的数组
  return new MyPromise((resolve, reject) => {
    //记录状态值为成功的实例对象数量
    let successPromiseNum = 0;
    //保存状态值为成功的实例对象的结果值
    const allPromiseResult = [];
    promiseList.forEach((item, index) => {
      item.then(
        (res) => {
          successPromiseNum++;
          //不使用push方法，根据索引值保存保证传入的实例对象的顺序和输出的结果值的位置一样
          allPromiseResult[index] = res;
          //判断数组的所有实例对象状态是否全部为成功
          promiseList.length === successPromiseNum && resolve(allPromiseResult);
        },
        (err) => reject(err)
      );
    });
  });
};
```
