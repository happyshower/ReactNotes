# 学习记录 8-21

### Promise

- promise 概念

  1. Promise 是 JavaScript 中一种处理异步操作的编程模式。它是一种用于处理异步操作的对象，可以将异步操作的结果（成功或失败）以一种更具可读性和可管理性的方式表示出来
  2. Promise 有三种状态:
     - Pending（进行中）：初始状态，表示异步操作正在进行中，还没有完成
     - Fulfilled（已完成）：表示异步操作已成功完成，可以获取到异步操作的结果
     - Rejected（已失败）：表示异步操作发生了错误或失败，无法获得预期的结果
  3. Promise 一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise 对象的状态改变，只有两种可能：从 pending 变为 fulfilled 和从 pending 变为 rejected。只要这两种情况发生，状态就不会再变了，会一直保持这个结果

- promise.prototype.then：then() 为 Promise 实例添加状态改变时的回调函数,该方法接收两个参数，且两个参数都是可选的，分别是成功( resolved / fulfilled 状态)的回调和失败( reject 状态)的回调，这两个回调都接受一个参数分别是成功的结果 result 和失败的结果 reason。
  ```js
  const promise = new Promise((resolve, reject) => {
    resolve("promise resolve test");
    //reject("promise reject test")
  });
  promise.then(
    (result) => {
      console.log(result); //promise resolve test
    },
    (reason) => {
      console.log(reason); //不执行
    }
  );
  ```
- promise.prototype.catch：用于指定发生错误时的回调函数，
  ```js
  const promise = new Promise((resolve, reject) => {
    throw new Error("Error test");
  });
  promise
    .then(
      (result) => {
        console.log(result); //不执行
      },
      (reason) => {
        console.log(reason); //不执行
      }
    )
    .catch((err) => {
      console.log(err); //error test，当出现错误时执行
    });
  ```
- promise.prototype.finally：用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法的回调函数不接受任何参数，无法获取 promise 状态的状态，即该方法的执行不依赖的 promise 的状态。

  ```js
  const promise = new Promise((resolve, reject) => {
    resolve("success");
    //reject("fail");
    //throw new Error("error test");
  });
  promise
    .then(
      (result) => {
        console.log(result); //success，仅当状态为 fulfilled 时执行
      },
      (reason) => {
        console.log(reason); //fail，仅当状态为 rejected 时执行，该方法执行后，不再执行catch
      }
    )
    .catch((err) => {
      console.log(err); //error test，当状态为 rejected 或 出现错误时执行
    })
    .finally(() => {
      console.log("promise finally log"); //promise finally log ，不依赖状态，一定执行
    });
  ```

- promise.race：用于在一组 Promise 中，只要有一个 Promise 成功（fulfilled）或失败（rejected），就返回一个新的 Promise。这个新的 Promise 的状态和值将与最先改变状态的的那个 Promise 相同

  ```js
  const promise1 = new Promise((resolve, reject) => {
    setTimeout(() => resolve("Promise 1 resolved"), 1000); // 定时器模拟异步任务，1秒后改变状态为 fulfilled
  });

  const promise2 = new Promise((resolve, reject) => {
    setTimeout(() => reject(new Error("Promise 2 rejected")), 500); // 定时器模拟异步任务，0.5秒后改变状态为 rejected
  });

  const promise3 = new Promise((resolve, reject) => {
    setTimeout(() => resolve("Promise 3 resolved"), 1500); // 定时器模拟异步任务，1.5秒后改变状态为 fulfilled
  });

  const racePromise = Promise.race([promise1, promise2, promise3]); //返回一个新的promise，且与第一个改变状态的 promise相同

  racePromise
    .then((result) => {
      console.log("First promise resolved:", result);
    })
    .catch((error) => {
      console.error("First promise rejected:", error.message); // First promise resolved: Promise 2 rejected
    });
  ```

- promise.all：用于在一组 Promise 全部成功（fulfilled）之后返回一个新的 Promise。这个新的 Promise 的成功值是一个包含所有输入 Promise 成功值的数组，顺序与传入的 Promise 数组的顺序一致

  ```js
  const promise1 = new Promise((resolve, reject) => {
    setTimeout(() => resolve("Promise 1 resolved"), 1000); // 定时器模拟异步任务，1秒后改变状态为 fulfilled
  });

  const promise2 = new Promise((resolve, reject) => {
    setTimeout(() => resolve("Promise 2 resolved"), 1500); // 定时器模拟异步任务，1.5秒后改变状态为 fulfilled
    //setTimeout(() => reject(new Error('Promise 2 rejected')), 1500);
    // 定时器模拟异步任务，1.5秒后改变状态为 rejected，并抛出一个错误 Error
  });

  const promise3 = new Promise((resolve, reject) => {
    setTimeout(() => resolve("Promise 3 resolved"), 500); // 定时器模拟异步任务，0.5秒后改变状态为 fulfilled
  });

  const allPromises = Promise.all([promise1, promise2, promise3]); //返回一个新的promise，该promise的成功值为包含所有成功值的数组

  allPromises
    .then((results) => {
      //仅当所有的promise都为resolve(fulfilled)时执行
      console.log("All promises resolved:", results);
      // All promises resolved: [ 'Promise 1 resolved', 'Promise 2 resolved', 'Promise 3 resolved' ]
    })
    .catch((error) => {
      //当有一个 Promise 失败（rejected）时，返回的 Promise 也是失败状态，并且错误信息与第一个失败的 Promise 相关联
      console.error("At least one promise rejected:", error);
    });
  ```
