# Js Event Loop
Js是單線呈語言(Single Thread)，一次只能執行一個任務

## Call Stack
又稱作Execution Stack，是一個紀錄目前程式執行狀態的空間
[loupe](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gZm4xKCkgew0KICBjb25zb2xlLmxvZygnZm4xJyk7DQp9DQoNCmZ1bmN0aW9uIGZuMigpIHsNCiAgZm4xKCk7DQogIGNvbnNvbGUubG9nKCdmbjInKTsNCn0NCg0KZnVuY3Rpb24gZm4zKCkgew0KICBmbjIoKTsNCiAgY29uc29sZS5sb2coJ2ZuMycpOw0KfQ0KDQpmbjMoKTs%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D) 可以用視覺化的方式理解流程

**ex:**
```javascript=
function fn1() {
  console.log('fn1');
}

function fn2() {
  fn1();
  console.log('fn2');
}

function fn3() {
  fn2();
  console.log('fn3');
}

fn3();
// 印出的順序為 fn1 -> fn2 -> fn3
```
該段程式碼的Callstack變化
![Callstack](https://meee.com.tw/vCO2xz5.gif)

執行到某一行的任務時就將其加入call stack中，執行完畢就移出
可以發現會優先執行**較晚被呼叫的function**

* function 的執行順序遵循「後進先出」（LIFO, Last In First Out）的模式
* 一次只能執行在 Call Stack 中最上方的一個任務


## 使用環境(Browser, node.js)的 Web APIs實現多線處理
**XMLHttpRequest(XHR)**, **setTimeout**, **setInterval**等 webAPIs協助處理需時較久的任務
當web api處理好，會先排入callback queue中等待call stack清空後才加入callStack中執行
![CallstackQueue](https://meee.com.tw/zSKLI64.png)

**EX2**
```javascript=
function fn1() {
  console.log('fn1');
}

function fn2() {
  console.log('fn2');
  fn1();
}

function fn3() {
  console.log('fn3');

  setTimeout(fn1, 1000);
 
  fn2();
}

fn3();
```
![CallstackQueue2](https://www.programfarmer.com/images/articles/javascript-browser-event-loop/03.gif)

可以看到
1. fn3 執行完畢，移出Stack後
1. 將 Queue 中存在的 fn1 移入 Stack 中執行
1. 印出 'fn1'，fn1 執行完畢，移出 Stack。

*setTimeout(fn1, 1000) 並非保證 fn1 一定會在 0.1s 後執行，因為倒數完 0.1s 後，只是將 fn1 排入 Callback Queue 等待，直到 Call Stack 為空時，才會再將 fn1 移入其中執行。因此只能說是「保證會等待至少 0.1s 後，才執行 fn1」。*

整個流程構成EventLoop

## Task & Microtask

### Task(Macrotask) 大型任務
包含但不限於:
* 解析HTML
* **執行 JavaScript 主線程式 (mainline)、script**
* setTimeout
* setInterval
* I/O、事件
* postMessage
* MessageChanne
* setImmediate (Node.js)

這些task被觸發後會排入特定TaskQueue, ex: setTimeout, setInterval → Timer Queue; Event → DOM Event Queue
**不同類型的大型任務，其處理優先順序，並沒有保證誰先觸發誰就先執行，要看瀏覽器如何實作**


### Microtask 微任務
包含但不限於:
* Promise then callback (executor 是同步的, async跟await是promise的語法糖)
* MutationObserver callback
... 

其callback會被放入Microtask Queue

### 運作流程
![eventLoop](https://www.programfarmer.com/images/articles/javascript-browser-event-loop/09.png)

1. 在一次的循環中，首先會先檢查 Task Queue 中，是否有 Task 存在(script也算)
2. 如果有 Task 就執行之，沒有就直接進入檢查 Microtask Queue
3. 當進行完一個 Task 後，會進入檢查 Microtask Queue 是否有 Microtask 微任務
4. 如果有 Microtask 就執行之，並且會將 Microtask Queue 中所有 Microtask 執行完畢後，才會進入下個 render 的階段
5. 如果有需要 render 就渲染，不需要就不執行。接著再回到第一步

**單次循環中，只處理一項大型任務 (Task)，但是所有微任務 (Microtask) 都會處理完畢。**

**ex:**
```javascript=
console.log('script start');

setTimeout(function () {
    console.log('setTimeout callback');
}, 1000);

new Promise(function (resolve, reject) {
    console.log('promise 1 resolve');
    resolve();
}).then(function () {
    console.log('promise 1 callback');
});

setTimeout(function () {
    console.log('setTimeout callback2222222222');
}, 500);

new Promise(function (resolve, reject) {
    console.log('promise 2 resolve');
    resolve();
}).then(function () {
    console.log('promise 2 callback');
});

console.log('script end');

// script start
// promise 1 resolve
// promise 2 resolve
// script end
// promise 1 callback
// promise 2 callback
// setTimeout callback2222222222
// setTimeout callback

```

該案例的執行順序:

第一次循環
1. 檢查Task，有script這個task，運行script
2. 執行promise的executor，印出promise 1 resolve，將promise的cb function放到Microtask
3. script這個task執行完畢後，檢查Microtask
4. 執行Microtask中的promise cb
5. render

第二次循環
1. 檢查task queue，執行先前setTimeout的cb
"setTimeout callback2222222222"
2. render

第三次循環
1. 檢查task queue，執行先前setTimeout的cb
"setTimeout callback"
2. render

**★多個setTimeout加入callstack的順序取決於計時器的時間**

## 結論
### Event Loop
Js執行的流程的循環，按照流程排序每次循環的執行順序
Task → Microtask → render
**每次循環只執行一個Task**

### 實現多工 Task
把需要額外處理的任務交給執行環境去處理
處理好後放入序列queue中，等待加入Callstack
其又分為:

#### 大型任務(Task/ Macrotask):
main script
event事件
setTimeout
setInterval....等

會依據Task的種類，會放入不同queue, ex: timer queue, DOMevent queue...
queue放入的順序依據環境的設定
#### 微任務(Microtask):
Promise....等

### Callstack
JS當前執行的主序列位置，後進先出(Last-In-First-Out)
每次循環開始會將Task放入，清空後再將Microtask放入並清空，之後結束循環
