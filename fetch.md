# 学习记录 7-20

## fetch

fetch()是 XMLHttpRequest 的升级版，用于在 JavaScript 脚本里面发出 HTTP 请求，且浏览器原生提供这个对象。

- fetch()的功能与 XMLHttpRequest 基本相同，但有三个主要的差异。

1. fetch()使用 Promise，不使用回调函数，简化了写法。

2. fetch()采用模块化设计，API 分散在多个对象上，如 Response 对象、Request 对象、Headers 对象。

3. fetch()通过数据流（Stream 对象）处理数据，可以分块读取，有利于提高网站性能表现，减少内存占用，对于请求大文件或者网速慢的场景相当有用。

- 用法：fetch()接收两个参数

1. 当 fetch 接收一个 URL 字符串作为参数时，默认发送 Get 请求
   ```
   //fetch的返回值是一个Promise对象
   //res是一个Response对象，需要使用 response.json()方法将 Response 对象的数据转为json对象
   //response.json()是一个异步操作，取出所有内容，并将其转为 JSON 对象。
   fetch(url)
   .then(res=>res.json())
   .then(json=>console.log(json))
   .catch(err=>console.log(err))
   ```
2. fetch 的第二个参数是一个配置对象，配置请求方式，请求头，请求体等
   ```
   //配置请求方式，请求体等
   const config = {
      method: "post",
      headers: {
        "Content-Type": "application/json",
      },
        body: JSON.stringify(data)     //将数据进行序列化
   }
   fetch(url，config)
   .then(res=>res.json())
   .then(json=>console.log(json))
   .catch(err=>console.log(err))
   ```

- async 和 await
  async 声明一个 function 来表示这个异步函数，await 用于等待函数中某个异步操作执行完成；await 只能用于 async 的内部，await 用于在一个异步操作之前，表示要等待这个异步操作的返回值，当使用了 await 时，只会阻塞 async 函数中的代码。因此，async 和 await 的结合实现了异步操作的同步写法。

```
const getData = async ()=>{
  let res = await fetch(url)
  let res2 = await fetch(url,options)  //必须等待上一个fetch返回结果后才执行
}

```

## Map 和 Set

- Set (集合)：接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化

1. Set 对象是一个类数组对象。
2. Set 对象存储的值是不重复的，通常使用它来实现数组去重。
3. Set 对象存储的数据不是键值对的形式，而且它可以存储任何类型的数据。

```
let arr = new Set()   //set集合初始化

let arr1 = [2,3,5,8,5,3,2]
let res = [...new Set(arr1)]   //结合扩展字符串，实现数组去重（经常使用）

let set = new Set(['red', 'green', 'blue']);
set.add('orange')   //向当前Set对象中添加一个值，返回的是Set对象，所以支持链式写法
set.has('blue')    //检测这个value是否是当前Set对象的一个元素，通过返回的布尔值表示
set.delete('orange')  //删除当前Set对象中的一个值，返回一个布尔值，表示是否删除成功
set.clear()   //清除当前集合

拓展：
Set.prototype.keys()：返回键名的遍历器
Set.prototype.values()：返回键值的遍历器
Set.prototype.entries()：返回键值对的遍历器
Set.prototype.forEach()：使用回调函数遍历每个成员
```

- Map（字典）

1. Map 对象这种数据结构和和对象类型，都以键值对的形式存储数据，即 key-vlue 形式。
2. Map 对象存储的数据是有序的，而平常使用的对象是无序的，所以通常当我们需要使用对象形式（键值对）存储数据且需要有序时，采用 Map 对象进行存储。
3. Map 对象的键值可以是任意类型，我们平时使用的对象只能使用字符串作为键。

```
//使用数字充当键
let errors = new Map([
  [400, 'InvalidParameter'],
  [404, 'Not found'],
  [500, 'InternalError']
]);

var map = new Map();
map.set(1, "black");    //设置键名key对应的键值为value，然后返回整个 Map 结构。如果key已经有值，则键值会被更新，否则就新生成该键
map.set("colors", 2);
map.get("colors")    //通过key读取key对应的键值，如果找不到key，返回undefined
map.delete(1)     //通过key删除某个键，返回true。如果删除失败，返回false
map.has(1)  //返回一个布尔值，表示某个键是否在当前 Map 对象之中

拓展：
Map.prototype.keys()：返回键名的遍历器。
Map.prototype.values()：返回键值的遍历器。
Map.prototype.entries()：返回所有成员的遍历器。
Map.prototype.forEach()：遍历 Map 的所有成员。
```
