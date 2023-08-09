# 学习记录 8-9

### web Worker

- Web Worker 为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行。在主线程运行结束后开启 worker 线程，web worker 是相当于后台运行，只有等主线程空闲了才会执行，等到 Worker 线程完成计算任务，再把结果返回给主线程。常应于计算密集型或高延迟的任务，不阻塞主线程页面 UI 渲染。
- 同源限制：分配给 Worker 线程运行的脚本文件，必须与主线程的脚本文件同源。
- DOM 限制：Worker 线程所在的全局对象，与主线程不一样，无法读取主线程所在网页的 DOM 对象。
- 通信限制：Worker 线程和主线程不在同一个上下文环境，它们不能直接通信，必须通过消息完成。
- 脚本限制：Worker 线程不能执行 alert()方法和 confirm()方法，但可以使用 XMLHttpRequest 对象发出的 AJAX 请求。
- 文件限制：Worker 线程无法读取本地文件，即不能打开本机的文件系统（file://），它所加载的脚本，必须来自网络。
  - onmessage：用于接受线程之间传递的数据，如主线程向 worker 线程传递数据和 worker 想主线程传递数据。该函数有一个参数 event，通过 event.data 获取数据。
  - postMessage：用于主线程或 worker 线程发送数据数据，如主线程向 worker 线程发送数据和 worker 向主线程发送数据。
  - terminate：关闭 worker 线程

```
** worker.js worker线程脚本文件**

/* eslint-disable no-restricted-globals */

const workercode = () => {
  //接受 index 发送过来的数据
  self.onmessage = function (e) {
    console.log("Worker received from main:", e.data);

    //模拟数据大量计算
    let i = 0;
    let res = 0;
    while (i < e.data) {
      res += i;
      i++;
    }
    //向 index 线程发送数据
    self.postMessage(res);
  };
};

// 把脚本代码转为string
let code = workercode.toString();
// 对字符串进行分割，取到worker执行的脚本
code = code.substring(code.indexOf("{") + 1, code.lastIndexOf("}"));
// 将字符串转换为Blob URL
const blob = new Blob([code], { type: "application/javascript" });
//创建一个字符串，其中包含表示参数中给定对象的 URL
const worker_script = URL.createObjectURL(blob);

export default worker_script;
```

```
** index.js 主线程**

import { useEffect, useState } from "react";
//引入 worker 线程脚本
import worker_script from "./worker.js";

const Demo = () => {
  const [workerInput, setWorkerInput] = useState(100000);
  const [workerResult, setWorkerResult] = useState();
  //模拟大量 UI 渲染
  const [list, setList] = useState(Array(1000).fill("li 渲染"));

  useEffect(() => {
    //创建一个worker线程
    const worker = new Worker(worker_script);
    //向 worker 线程发送数据
    worker.postMessage(workerInput);
    //接受 worker 发送过来的数据
    worker.onmessage = function (event) {
      setWorkerResult(event.data);
      console.log("Main received message", event.data);
    };

    return () => {
      //关闭worker线程
      worker.terminate();
    };
  });

  return (
    <>
      <h2>向 worker 传入的数据：{workerInput}</h2>
      <h2>worker 返回的数据：{workerResult}</h2>
      <ul>
        {list.map((item, index) => (
          <li key={index}>
            {item} -- {index}
          </li>
        ))}
      </ul>
    </>
  );
};
export default Demo;

```
