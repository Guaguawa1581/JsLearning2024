# Part1 作用域和閉包
## ch. 1 作用域Scope
### 1. 編譯原理
code在執行前3個步驟，統稱編譯
#### 編譯流程
```flow
st=>start: start
op=>operation: 分詞 / 詞法分析
op2=>operation: 解析
op3=>operation: 代碼生成
e=>end: end
st->op->op2->op3->e
```
##### 分詞 / 詞法分析（Tokenizing/Lexing）
將字符串分解成有意義的代碼塊token
```javascript
var a = 2 ;
// 拆分成 var, a, =, 2, ;
```
如果 tokens 的排列不符合語法規則，解析器將拋出錯誤（例如語法錯誤）。

##### 解析（Parsing）
構建抽象語法樹（AST），將代碼中的語法結構轉化為樹狀結構，其中每個節點代表代碼中的一個構造（如語句、運算符、表達式等）
★: **抽象語法樹(Abstract Syntax Tree)**

範例1 
```javascript
let x = 5 + 3;
```
該程式碼的AST大致如下
```lua
Program //根節點，代表整個程式
  |
  +--VariableDeclaration //表示變量聲明,包含了一個或多個變量宣告
     |
     +--declarations: [VariableDeclarator] //表示單個變量的宣告
        |
        +--id: Identifier (name: 'x') //表示變量名（這裡是 'x'）
        |
        +--init: BinaryExpression //二元運算表達式
            |
            +--left: Literal (value: 5) //表示數字 5
            |
            +--operator: '+' //表示運算符 '+'
            |
            +--right: Literal (value: 3) //表示數字 3

```

範例2
```javascript
function add(a, b) {
    return a + b;
}
```
AST
```lua
Program
  |
  +--FunctionDeclaration //表示函數聲明
     |
     +--id: Identifier (name: 'add') //表示函數名（這裡是 'add'）
     |
     +--params: [Identifier (name: 'a'), Identifier (name: 'b')] //params包含多個 Identifier 的陣列，表示函數參數
     |
     +--body: BlockStatement //表示函數體，包含一系列語句
        |
        +--body: [ReturnStatement] //表示一個返回語句
           |
           +--argument: BinaryExpression //作為 ReturnStatement 的子節點，表示二元運算
               |
               +--left: Identifier (name: 'a')
               |
               +--operator: '+'
               |
               +--right: Identifier (name: 'b')


```

##### 代碼生成（code generation）
AST 是後續編譯階段（如轉換、優化）的基礎。
編譯器將使用 AST 來生成執行代碼或直接執行代碼。

**在 JavaScript 中的應用**
- 在 JavaScript 中，像 Babel 這樣的轉譯器會使用 AST 來轉換 ES6+ 的代碼為向下兼容的 JavaScript 版本。
- JavaScript 引擎（如 V8）使用 AST 進行優化和生成可執行的機器碼。

### 2. 引擎、編譯器、作用域
角色：引擎、編譯器、作用域

編譯器使用AST，在**作用域**中編譯
引擎在**作用域**中執行查找

作用域是一套規則，用於確定在何處以及如何尋找變數（標識符）。
- 如果查找的目的是對變數進行賦值，那麼就會使用 **LHS** 查詢
- 如果目的是取得變數的值，就會使用 **RHS** 查詢
- **作用域嵌套**：引擎從目前的執行作用域開始尋找變量，如果找不到，
就向上一級繼續找。 當抵達最外層的全域作用域時，無論找到或沒找到，查找過程都
會停止。

引擎與作用域對話
```javascript
var a = 2 ;
```
在這裡引擎為變數a進行LHS查詢，
```javascript
function foo(a) {
var b = a;
return a + b;
}
var c = foo( 2 );
```
RHS： foo、=a、a+、+b
LHS： (a)、var b=、var c=






**LHS**
試圖找到變量的容器本身
目的在於賦值
test(2) // 2  這裡是a=2的隱式的LHS

    '小明' = 1 ; //SyntaxError
當出現 LHS 錯誤訊息時，
要特別注意是不是左邊的變數不能被賦予值。
此錯誤屬於**編譯過程中的錯誤**。

**RHS**
"非左側"
賦值的來源或計算結果
目的在於查找變量值、引用
```javascript
console.log(a);
            ^
ReferenceError: a is not defined
```
RHS 不會在編譯過程中出現錯誤，他會在**執行階段**才發現這個變數無法取得
RHS查詢失敗引擎會拋出**ReferenceError**
```javascript
let obj;
console.log(obj.property); // TypeError: Cannot read property 'property' of undefined
                ^
```
RHS 查詢找到一個變量，但嘗試對這個變量的值進行不合理的操作，
引擎會拋出另一種類型的異常，叫做 **TypeError**

------------
## ch.2 詞法作用域
作用域的種類：詞法作用域(靜態作用域)、動態作用域

|類別       | 詞法作用域 (Lexical Scope)  | 動態作用域 (Dynamic Scope)  |
|  :-------:| :-------------------------:| :-------------------------:|
| 定義      | 作用域是在代碼編寫時確定的，取決於函數被聲明的位置。 | 作用域是在代碼執行時確定的，取決於函數被調用的方式。 |
| 主要語言  | 大多數現代程式語言，如 JavaScript、Python、C、Java。  | Bash 脚本、Perl 中的一些模式等  |

### 1.詞法階段
**查找**
作用域查找會在找到第一個符合的識別碼時停止。 在多層的巢狀作用域中可以定義同名的標識符，
這叫作「遮蔽效應」（內部的標識符「遮蔽」了外部的標識符）。
```javascript
var obj = { a: 12, b: 13 };
function ttt() {
  obj = { a: 15 };
  console.log(obj.a, obj.b); // 15 undefined
}
ttt();
```

#### 欺騙詞法 eval()
接受一個字符串參數，並將其當作 JavaScript 代碼來執行
```javascript
function foo(str, a) {
eval( str ); // 欺骗！
console.log( a, b );
}
var b = 2;
foo( "var b = 3;", 1 ); // 1, 3
```
- 安全性：由於 eval 可以執行任何代碼，因此它可能執行惡意代碼，尤其是當用於執行不受信任的輸入時 (XSS)。
- 性能：引擎無法對 eval 中的代碼進行優化。
- eval 函數的實現是 JavaScript 引擎的一部分，並且通常是用底層語言如 C++ 編寫的。這些實現是引擎的核心組件，不是用 JavaScript 編寫的，也不可在 JavaScript 代碼中修改。

#### with()
with 語句將一個物件加入到作用域鏈的頂部。在 with 塊內，你可以直接引用該物件的屬性而無需重複寫出物件名稱。
```javascript
function foo(obj) {
  with (obj) {
    a = 2;
  }
}
var o1 = {
  a: 3
};
var o2 = {
  b: 3
};
foo(o1);
foo(o2);
console.log(o1.a); // 2
console.log(o2.a); // undefined
console.log(a); //2
```
with 語句是一個用於擴展局部作用域的語法結構。
它可以將一個物件的屬性添加到當前作用域中，使得可以直接訪問該物件的屬性，而不需要重複引用該物件

**foo(o2)過程**
在 foo 函數中，a = 2; 這一行首先在 with 的作用域（也就是 obj 的屬性）中尋找 a。
如果 obj 有 a 屬性，則賦值操作會應用於 obj.a。
如果 obj 沒有 a 屬性，則 JavaScript 引擎會向外層作用域查找 a。
全局變量的創建：
對於 o2，由於它沒有 a 屬性，a = 2; 將導致 JavaScript 引擎在外層作用域查找 a。
由於 a 也不是 foo 函數內的局部變量（沒有使用 var、let 或 const 聲明），JavaScript 引擎會繼續在全局作用域中查找 a。
最終，由於全局作用域中也沒有找到 a，引擎會在全局作用域中創建一個新的全局變量 a 並賦值為 2。這就是為什麼 console.log(a); 能夠輸出 2。
**嚴格模式不能使用、也不推薦使用，作用域混亂**
------
## Ch.3 函數作用域與塊作用域
### 3.1函數作用域(局部作用域)
限定在function(){}中的作用域
最小暴露原則：利用函數作用域的特性，盡量隱藏作用域內部的變數及函數
好處：規避命名衝突
```javascript
function foo() {
	function bar(a) {
	i = 3; // 修改 for 循环所属作用域中的 i 
	console.log( a + i );
	}
	for (var i=0; i<10; i++) {
	bar( i * 2 ); // 糟糕，无限循环了！
}}
foo();
```
改成var i = 3; 避免衝突


函數聲明：function是聲明中的第一個詞，其他則是函數表達式

IIFE立即執行函數
(function(){...})()
**常用方式**
1. 匿名函式
2. 當作函式調用同時傳參數進去
```javascript
(function IIFE(global) {
  var a = 3;
  console.log(a); // 3
  console.log(global.a); // 2
})(window);
```

### 3.2 執行環境 & 變數物件
在函式生成時會產生該函式的**執行環境（Execution Context, EC）**
EC生成時又會有相對應的**變數物件（Variable Object, VO）**，宣告的變數及函式都會被儲存在 VO 裡面。
VO 另外稱作**執行物件（Activation Object, AO）**, AO跟 VO 一樣，差別在於 AO 除了儲存 {} 內的變數還儲存了 () 內的參數，因為 AO 是對應函式的 EC 而存在，所以不用宣告參數也會在函式的作用域裡面。

### 4. 塊作用域
ES6新增塊作用域，切分最小有效範圍 
let, const宣告變數
#### WHAT IS 
區塊指的是 {...} 大括號範圍內的區域都稱之區塊，像是 if 及 switch 判斷式、while 及 for 迴圈，
包括 function 的大括號範圍都是區塊。
#### 迴圈中的小括號 () 
```javascript
//EX1:
for (var j = 0; j < 5; j++) {
    console.log(j); //output: 0 1 2 3 4
}
console.log(j);     // output: 5

//EX2:
for (let i = 0; i < 5; i++) {
    console.log(i); //output: 0 1 2 3 4
}
console.log(i);     // ReferenceError: i is not defined
```
由此可知()內宣告變數，是一個**區塊變數**
forLoop中 () 內宣告的變數會傳入後面的 {}，因為這是 for 循環的設計方式，旨在為循環的每次迭代提供必要的變數和條件。
forLoop每次迭代都會建立一個新的環境（context），而這個環境會紀錄當下的變數 i 值，不會覆蓋掉上一個環境裡面的變數值，因此可以產生多個 i 值。








## Ch.4 提升 hoisting
### waht is
提升（Hoisting）是指變數宣告和函數宣告在程式碼執行前被提升到它們所在作用域的頂部的行為。 
這意味著無論聲明在程式碼的哪個位置，它們都會被移至其作用域的最開始部分。
變量跟函數的聲明會在代碼執行前首先處理
### 1. 變數的狀況
定義聲明在編譯階段進行，賦值聲明在執行階段，**先聲明後賦值**
```javascript
foo();
function foo() {
console.log( a ); // undefined
var a = 2;
}

//相等於
function foo() {
var a;
console.log( a ); // undefined
a = 2;
}
```

### 2. 函數的狀況
函數又優先於變量
```javascript
foo(); // 1
var foo;
function foo() {
console.log( 1 );
}
foo = function() {
console.log( 2 );
};

//相等於
function foo() {
console.log( 1 );
}
foo(); // 1
foo = function() {
console.log( 2 );
};
```
相同的函數聲明可以覆蓋前面的
```javascript
foo(); // 3
function foo() {
console.log( 1 );
}
var foo = function() {
console.log( 2 );
};
function foo() {
console.log( 3 );
}
```

## Ch.5 作用域閉包
### What is 
函式以及該函式被宣告時所在的作用域環境 (lexical environment) 的組合，
閉包就是內部函式能夠取得函式外部的變數，並且記住這個變數
```javascript
function outer() {
  let a = 0;
  function inner() {
    a += 1;
    console.log(a);
  }
  return inner;
}

const inner = outer();

inner(); // 1
inner(); // 2
inner(); // 3
```
在 outer() 執行後，通常會預期 outer() 的整個內部作用域都會被銷毀
閉包阻止這件事情的發生，內部作用域依然存在，inner() 在使用outer()這個作用域本身

### 閉包應用
#### 1. 狀態保存
利用保存的特性，記憶狀態的改變
```javascript
function useState(initialState) {
  let state = initialState;

  function getState() {
    return state;
  }

  function setState(updatedState) {
    state = updatedState;
  }
  return [getState, setState];
}

const [count, setCount] = useState(0);

count(); // 0
setCount(1);
count(); // 1
setCount(500);
count(); // 500
```
#### 2. 模塊
ES6透過import引進運用閉包特性的模塊
```javascript
// bar.js
function hello(who) {
return "Let me introduce: " + who;
}
export hello;
// foo.js
// 仅从 "bar" 模块导入 hello()
import hello from "bar";
var hungry = "hippo";
function awesome() {
console.log(
hello( hungry ).toUpperCase()
);
}
export awesome;

// baz.js
import foo from "foo";
import bar from "bar";
console.log(
bar.hello( "rhino" )
); // Let me introduce: rhino
foo.awesome(); // LET ME INTRODUCE: HIPPO
```
### 閉包缺點
**內存洩漏：** 閉包會讓內部函式記得外部的變數，這可能會造成變數常駐在記憶體中



## 附錄A 動態作用域
#### 詞法作用域（Lexical Scoping）
同靜態作用域
變量的作用域由源代碼中的位置決定，編寫時就已經確定，而不是在代碼執行時。
#### 動態作用域（Dynamic Scoping）
變量的作用域是在代碼運行時決定的，基於變量使用時的呼叫棧。
