### Ch.3 提升Hoisting
#### WHAT IS 提升(Hoisting)
Javascript 在執行任何程式碼之前，會先把宣告的變數和函式放進記憶體空間裡，提升到程式的第一行
**只有宣告， 賦值初始化(Initialization) 不在其中**

#### 3.1 提升的特性
1. 變數的宣告 (Declarations) 會被提升
```javascript
a=1;
console.log(a); // 1
var a;
```
2. 函式陳述式 (function declaration) 會被提升
```javascript
callMe('emma');  // hello emma
function callMe(name){
  console.log('hello '+name);
}
```
3. 函式運算式 (function expression) 不會被提升
```javascript
console.log(myName);           // undefined
myName('emma');                // myName is not a function
var myName = function (name) {
    console.log('i am + name');
};
```
這種以 var 來宣告的函式，稱為函式運算式，宣告會被提升，但是函式的內容不會

4. 函式優先
```javascript
console.log(aa);  // fn a(){}
var aa;
function aa(){};
```

5. 多個函式同名，後面的會覆蓋前面的
```javascript
aa(); //222222
function aa() { console.log("11111"); };
function aa() { console.log("22222"); };
```

#### 3.2 let,const的提升
let&const雖會被提升，但在初始化(Initialization)前，會進入暫時死區(Temporal dead zone)不能使用會ReferenceError
```javascript
console.log(aa); // undefined
var aa = 1111;

console.log(bb); // ReferenceError 'bb'
const bb = 22222;
```