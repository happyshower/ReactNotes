# 学习记录 8-7

### 数组的拓展

- Array.from()：用于将两类对象转为真正的数组：类似数组的对象和可遍历的对象。该方法接受两个参数，第一个参数为数组或对象，第二个参数为一个函数。

  ```
  //类数组对象，必须带有 length 属性
  let obj = { 0: "a", 1: "b", 2: "c", length: 3 };
  const arr = new Array.from(obj);   //["a","b","c"]

  //返回一个新的数组，但当该参数重包含引用对象时，属于浅拷贝
  new Array.from([1,2,3])   //[1,2,3]

  //提供类似于 map 的方法
  new Array.from([1,2,3],(n)=> n*2 )  //[2,4,6]

  //提供一个包含length属性的对象，利用第二个参数，返回一个长度为 length 的数组
  new Array.from({ length: 5 }, () => 0) //[0,0,0,0,0]
  ```

- Array.find()；用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为 true 的成员，并返回该成员。
  ```
  [1,5,8,9].find((item) => item > 2)   //[5,8,9]
  ```
- Array.fill()：使用指定值，填充一个数组。

  ```
  //覆盖掉原来的元素
  [1,2,3].fill(0);   //[0,0,0]

  //生成并填充一个长度为 3 且全为 0 的数组
  new Array(3).fill(0)   //[0,0,0]

  //从索引为0的元素开始到索引为2的元素之前结束，替换为0
  [2,3,3].fill(0,0,2)  //[0,0,3]

  //填充一个长度为3内容为{num:2}的数组，填充的类型为对象，被赋值的是同一个内存地址的对象，属于浅拷贝
  new Array(3).fill({num: 2}) //[{num: 2},{num: 2},{num: 2}]
  ```

- Array.includes()：返回一个布尔值，表示某个数组是否包含给定的值。该方法接受两个参数，第一个参数为判断的元素，第二个参数表示搜索的起始位置

  ```
  //判断该数组是否包含该元素
  [1,2,3].inludes(2);   //true

  //从该数组索引为2的位置开始判断是否包含该元素
  [1,2,3，4，5].inludes(2，2);   //false
  ```

- Array.flat()：用于将嵌套的数组“拉平”，变成一维的数组并返回一个新数组.

  ```
  //判断该数组是否包含该元素
  [1,2,3,[4,5,[6]]].flat();   //[1,2,3,4,5,[6]]

  //2表示要拉平的嵌套层数，当该参数为infinity时，将该数组全部拉平
  [1,2,3,[4,5,[6]]].flat(2);   //[1,2,3,4,5,6]
  ```

### 对象的拓展

- Object.assign()：用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target），Object.(target,source1,source2)。源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用，属于浅拷贝。
  ```
  //将{a:1},{b:2}合并到目标对象{}中
  Object.assign({},{a:1},{b:2})  //{a:1,b:2}
  ```
- Object.Freeze()：静态方法冻结对象。冻结对象会阻止扩展并使现有属性不可写和不可配置且冻结的对象无法再更改。
  ```
  const obj = { prop: 42, };

  Object.freeze(obj);

  obj.prop = 33;     //error,不能修改冻结对象
    ```
- Object.is()：用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致
  ```
  +0 === -0 //true
  NaN === NaN // false

  Object.is(+0, -0) // false
  Object.is(NaN, NaN) // true
  ```
- Object.keys()，Object.values()，Object.entries()：配套使用，作为遍历一个对象的补充手段。
  ```
  let obj = { a: 1, b: 2, c: 3 };

  //Object.keys()，返回一个数组，成员是参数对象自身的所有可遍历属性的键名
  for (let key of Object.keys(obj)) {
    console.log(key); // 'a', 'b', 'c'
  }

  //Object.values(),返回一个数组，成员是参数对象自身的所有可遍历属性的键值
  for (let value of Object.values(obj)) {
    console.log(value); // 1, 2, 3
  }

  //Object.entries(),返回一个数组，成员是参数对象自身的所有可遍历属性的键值对数组
  for (let [key, value] of Object.entries(obj)) {
    console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
  }
  ```
