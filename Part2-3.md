### Ch.3 對象
this根據函數調用位置的不同，會造成this綁定的對象不同，而這裡的對象又是什麼?
#### 3.1 原始型別 Primitive types
- string	
文字,例如 "hello"
- number 	
整數或浮點數，例如 123 或 3.14
- boolean	
兩個值true or false
- null	
空值
- undefined	
變量未被賦值
- symbol	
(ES6新增)表示唯一的、不可變的資料值，常用於作為物件屬性的key
- bigint	
(ES2020引入)表示大於 2^53 - 1 的整數

**typeof null會返回"object"，此為bug，實際上null本身是基本類型**

原始型別沒有方法，但仍然表現得像有方法。 當在原始值上存取屬性時，JavaScript 會自動將值裝入包裝物件中，並存取該物件上的屬性。 例如，"foo".includes("f") 隱式建立了一個 String 包裝對象，並在該物件上呼叫 String.prototype.includes()。
這邊自動將primitiveString包裝成，String()物件對象
使之得可以使用 String.prototype.toUpperCase() 方法。
這個操作完之後，包裝對象被丟棄，primitiveString 仍然是原始類型。
```javascript
// 原始字符串
let primitiveString = "Hello, world!"; // string
// 自動包裝成對象String()，並使用 String.pototype上的方法 .toUpperCase()
console.log(primitiveString.toUpperCase()); // 輸出: "HELLO, WORLD!"
```

##### 關於symbol
```javascript
// 唯一性
let symbol1 = Symbol('description');
let symbol2 = Symbol('description');
console.log(symbol1 === symbol2); // 輸出: false

// 物件屬性鍵
let mySymbol = Symbol();
let obj = {
    mySymbol: "value"
};
console.log(obj.mySymbol); // 輸出: value
```

##### 簡單基本類型並不是對象, 且 Primitive Types 以 by value的方式去處理
**By value的意思**
y的值是從x值來的，所以當y值做任何變動都不會影響到x值,
x y 比較也是以真實的值進行互相比較
```javascript
let x = 2
// Copy by value
let y = x
y += 5
console.log(y, x)
// 7, 2
```

#### 3.2 內置對象(物件型別) Object types
Object types和Primitive types不同的是，物件是一種複合值：它可以將多個原始值或其他物件聚合在一起，可透過鍵名(key)存取這些值。
- String
- Number
- Boolean
- Object
- Function
- Array
- Date
- RegExp
- Error

不同的物件在底層都表示為二進制，在 JavaScript 中二進位前三位都為 0 的話會被判
斷為 object 類型，null 的二進位表示是全 0，自然前三位也是 0，所以執行 typeof 時會傳回“object” 
```javascript
var strPrimitive = "I am a string"; // 原始值
console.log(typeof strPrimitive);  // "string"
console.log(strPrimitive instanceof String); // false

var strObject = new String("I am a string"); // 內置對象
console.log(typeof strObject); // "object"
console.log(strObject instanceof String); // true 
```
原始值 "I am a string" 並不是一個對象，它只是一個原始值，並且是一個不可變的值。
如果要在這個原始值上執行一些操作，例如取得長度、存取其中某個字元等，那需要將其轉換為 String 對象

ex2:
```javascript
var strPrimitive = "I am a string"; 
console.log( strPrimitive.length ); // 13
console.log( strPrimitive.charAt( 3 ) ); // "m"
```
引擎自動把原始值轉換成String對象

##### Object types 是以 by reference 在操作
**By reference 的意思**
by reference 指的是當物件型別在操作時，其實底層是對實體記憶體為製作操作，當宣告一新變數 arr2 並將 arr1 賦值給 arr2時，其實是把記憶體位置給 arr2 所以任何對 arr2 的操作都會連動到原本 arr1 的值
```javascript
// compare by reference
let arr1 = [],
    arr2 = arr1

arr2[0] = 'PK'

console.log(arr1[0]) // 'PK'
console.log(arr1 === arr2) // true

// 宣告兩個 objective type 讓 key-value 值完全相同，但是兩者比較後，回傳是 false ，因為 by reference 在比較時，真正比的是兩者的位置，所以個別的 Array 、Object 永遠不會相同。
let obj1 = { x: 1 },
    obj2 = { x: 1 }

console.log(obj1 === obj2) // false
```



#### 3.3 屬性描述符
##### 3.3.1 WHAT IS
ES5後所以屬性具備了屬性描述符，對象的屬性數據隱含了三個特性:writable(可寫性), enumerable(可枚舉), configurable(可配置)，
透過Object.defineProperty(..)新增修改屬性的特性
```javascript
var myObject = {
a:2
};
Object.getOwnPropertyDescriptor( myObject, "a" );
// {
// value: 2,
// writable: true,
// enumerable: true,
// configurable: true
// }
```

1.writable 可寫性
決定能否修改屬性的值

```javascript
var myObject = {};
Object.defineProperty( myObject, "a", {
value: 2,
writable: false, // 不可寫！
configurable: true,
enumerable: true
} );
myObject.a = 3;
myObject.a; // 2
```
静默失败（silently failed）
**嚴格模式** 會 TypeError

2.configurable 可配置
決定之後能否再更改設定, 後面再更改設定或delete，會TypeError

```javascript
var myObject = {
    a: 2
};
myObject.a = 3;
myObject.a; // 3
Object.defineProperty(myObject, "a", {
    value: 4,
    writable: true,
    configurable: false, // 不可配置！
    enumerable: true
});
myObject.a; // 4
myObject.a = 5;
myObject.a; // 5

Object.defineProperty(myObject, "a", {
    value: 6,
    writable: true,
    configurable: true,
    enumerable: true
}); // 又更改設定 TypeError
```



3.enumerable 可枚舉
決定屬性是否會在for...in..循環中出現

```javascript
const obj = {
    property1: 'Value 1',
    property2: 'Value 2',
    property3: 'Value 3'
};

// 將property2的enumerable屬性設置為false
Object.defineProperty(obj, 'property2', {
    enumerable: false
});

for (let key in obj) {
    console.log(key); // 只會列舉property1和property3
}
```

##### 3.3.2 不變性
1.Object.preventExtensions(obj)
禁止擴展
```javascript
const obj = { prop1: 'value1', prop2: "value2" };

Object.preventExtensions(obj);

obj.prop1 = 'newvalue1'; // 修改現有屬性
obj.prop3 = 'value3'; // 無法添加新屬性
delete obj.prop1; // 可以刪除現有屬性
console.log(obj); // {prop2: 'value2'} 
```
2.Object.seal(obj)
在對象上調用Object.preventExtensions()並將 configurable : false
```javascript 
const obj = { prop1: 'value1' };

Object.seal(obj);

obj.prop2 = 'value2'; // 無法添加新屬性
obj.prop1 = 'newvalue1'; // 可以修改現有屬性
delete obj.prop1; // 無法刪除現有屬性
console.log(obj); //{ prop1: 'newvalue1' }
```

3.Object.freeze(obj)
在對象上調用Object.seal()並將 writable : false
```javascript 
const obj = { prop1: 'value1' };

Object.freeze(obj);

obj.prop2 = 'value2'; // 無法添加新屬性
obj.prop1 = 'newvalue1'; // 無法修改現有屬性
delete obj.prop1; // 無法刪除現有屬性

console.log(obj); //{ prop1: 'value1' }
```

##### 3.3.3 Getter和Setter
在 ES5 中可以使用 getter 和 setter 部分來改寫預設操作，但只能套用在單一屬性上
getter 是一個隱藏函數，在取得屬性值時呼叫。
setter 也是一個隱藏函數，在設定屬性值時呼叫。
```javascript
var myObject = {
    // getter
    get a() {
        return this._a_;
    },
    // setter
    set a(val) {
        this._a_ = val * 2;
    }
};
myObject.a = 2;
console.log(myObject.a); // 4
```
不管是物件文字語法中的 get a() { .. }，還是 defineProperty(..) 中的明確定義，二者都會在物件中建立一個不包含值的屬性(範例中的a)

##### 3.3.4 存在性
Object.hasOwnProperty(屬性)可以檢查是否有這個屬性
```javascript
var myObject = {
    a: undefined
};
console.log(myObject.a);  // undefined
console.log(myObject.b);  // undefined
console.log(myObject.hasOwnProperty("a"));  // true
console.log(myObject.hasOwnProperty("b"));  // false
```

##### 3.3.5 枚舉性
enumerable 控制該屬性是否能被枚舉(可以出現在物件屬性的loop中)

```javascript
var myObject = {};
Object.defineProperty(
    myObject,
    "a",
    { enumerable: true, value: 2 }
);
Object.defineProperty(
    myObject,
    "b",
    { enumerable: false, value: 3 }  // 讓 b 不能枚舉
);
console.log(myObject.b); // 3
console.log("b" in myObject); // true
console.log(myObject.hasOwnProperty("b")); // true

for (var k in myObject) {
    console.log(k, myObject[k]);
} // "a" 2  沒有"b"
console.log(myObject.propertyIsEnumerable("a")); // true
console.log(myObject.propertyIsEnumerable("b")); // false
console.log(Object.keys(myObject)); // ["a"]
console.log(Object.getOwnPropertyNames(myObject)); // ["a", "b"]
```
**Object.propertyIsEnumerable( "a" ) 區分屬性是否可以枚舉**


#### 3.4 深複製(deep copy) & 淺複製(shallow copy)
JS 中，資料的型別主要有分為「基本型別 (Primitive type)」 以及「物件 (Object)」，兩者最大的差異在於：

**Primitive type data**
複製變數時，會直接「 複製值 (value) 」。像是：string、number、boolean、undefined、null、symbol 的變數資料。

**Object data**
複製變數時，會「 複製地址 (address) 」。像是：object、array、function 的變數資料。
```javascript
/*** 基本型別 ***/
let a = 5;
let b = a; // 複製 primitive data 變數

console.log(a); // 5
console.log(b); // 5

b = 10;

console.log(a); // 5 => 沒被改變，因為 a 與 b 的值不同
console.log(b); // 10
```

```javascript
/*** 物件型別，改變內容 ***/
let a = { number: 5 };
let b = a; // 複製 object data 變數

console.log(a); // { number : 5 }
console.log(b); // { number : 5 }

b.number = 10;

console.log(a); // { number : 10 } => 跟著改變，因 a 與 b 地址相同，指向同個值
console.log(b); // { number : 10 }
```

##### 淺拷貝的方法
只要有任何一層的資料地址相同，換句話說就是「只要並非兩個完完全全獨立的 Object data」，就依然是淺拷貝。

1.自建 shadowCopy 函式，複製第一層物件值

```javascript
/*** 淺拷貝：自建 shadowCopy 函式，複製第一層物件值 ***/
function shadowCopy(originalObj) {
  let clonedObj = {};
  for (const key in originalObj) {
    clonedObj[key] = originalObj[key];
  }
  return clonedObj;
}

const originalData = {
  firstLayerNum: 10,
  obj: {
    secondLayerNum: 100,
  },
};
const clonedData = shadowCopy(originalData);
clonedData.firstLayerNum = 20; // 更改clonedData內的值
clonedData.obj.secondLayerNum = 200;

console.log(originalData.firstLayerNum);
// 10 => 第一層'沒有'被 clonedData 影響
console.log(originalData.obj.secondLayerNum);
// 200 => 第二層被 clonedData 影響而改變
```

2.使用Object.assign(target, ...sources)
assign()的功用是合併物件，也可以使用在淺層複製物件

```javascript
/*** 淺拷貝：Object.assign ***/
const originalData = {
  firstLayerNum: 10,
  obj: {
    secondLayerNum: 100,
  },
};
const clonedData = Object.assign({}, originalData);
clonedData.firstLayerNum = 20; // 更改clonedData內的值
clonedData.obj.secondLayerNum = 200;

console.log(originalData.firstLayerNum);
// 10 => 第一層沒有被 clonedData 影響
console.log(originalData.obj.secondLayerNum);
// 200 => 第二層被 clonedData 影響而改變
```

3.Spread operator
```javascript
/*** 淺拷貝：Spread operator ***/
const originalData = {
  firstLayerNum: 10,
  obj: {
    secondLayerNum: 100,
  },
};
const clonedData = { ...originalData };

clonedData.firstLayerNum = 20; // 更改clonedData內的值
clonedData.obj.secondLayerNum = 200;

console.log(originalData.firstLayerNum);
// 10 => 第一層沒有被 clonedData 影響
console.log(originalData.obj.secondLayerNum);
// 200 => 第二層被 clonedData 影響而改變
```

4.部分 Array 方法，如：slice()、from() 等
```javascript
/*** 淺拷貝：Array.prototype.slice() ***/
const originalData = [10, { secondLayerNum: 100 }];
const clonedData = originalData.slice();

clonedData[0] = 20;
clonedData[1].secondLayerNum = 200;

console.log(originalData[0]);
// 10 => 第一層沒有被 clonedData 影響
console.log(originalData[1].secondLayerNum);
// 200 => 第二層被 clonedData 影響而改變
```

##### 深拷貝的方法
當 Original Object data 與 Cloned Object data ，是兩個完全獨立，每一層的資料地址都不同，相互不影響的深層物件

1.JSON.stringify/parse
用 JSON.stringify 先把物件轉字串，再用 JSON.parse 把字串轉物件

```javascript
/*** 深拷貝：JSON.stringify/parse ***/
const originalData = {
  firstLayerNum: 10,
  obj: {
    secondLayerNum: 100,
  },
};
const clonedData = JSON.parse(JSON.stringify(originalData));

clonedData.firstLayerNum = 20; //更改clonedData內的值
clonedData.obj.secondLayerNum = 200; //更改clonedData內的值

console.log(originalData.firstLayerNum);
// 10 => 第一層「沒有」被 clonedData 影響
console.log(originalData.obj.secondLayerNum);
// 100 => 第二層「沒有」被 clonedData 影響
```
**JSON.stringify/parse 注意**
- undefined : 會連同 key 一起消失。
- NaN : 會被轉成 null。
- Infinity :會被轉成 null。
- regExp : 會被轉乘 空{}。
- Date : 型別會由 Data 轉成 string。

```javascript
const originalData = {
  undefined: undefined, // undefined values will be completely lost, including the key containing the undefined value
  notANumber: NaN, // will be forced to null
  infinity: Infinity, // will be forced to null
  regExp: /.*/, // will be forced to an empty object {}
  date: new Date('1999-12-31T23:59:59'), // Date will get stringified
};
const faultyClonedData = JSON.parse(JSON.stringify(originalData));

console.log(faultyClonedData.undefined); // undefined type:undefined
console.log(faultyClonedData.notANumber); // null type:object
console.log(faultyClonedData.infinity); // null type:object
console.log(faultyClonedData.regExp); // {} type:object
console.log(faultyClonedData.date); // "1999-12-31T15:59:59.000Z" type:string
```

2.遞迴式深拷貝Function
```javascript
function deepClone(obj, map = new WeakMap()) {
    // 確認是否循環引用
    if (map.has(obj)) {
        return map.get(obj);
    }
	
    // 確認是不是原始型態，如果是的話就直接回傳
    if (obj === null || typeof obj !== "object") {
        return obj;
    }

    // 處理 Date 和 RegExp 這種特殊型態
    if (obj instanceof Date) {
        return new Date(obj.getTime());
    }

    if (obj instanceof RegExp) {
        return new RegExp(obj);
    }

    // 看 obj 是物件還是陣列，然後先建一個新的空物件 (或空陣列)
    let result = Array.isArray(obj) ? [] : {};

    // 在 WeakMap 中添加對象的引用，標記為已處理
    map.set(obj, result);

    // 透過 Object.entries 來迭代，然後遞迴地對每個值深拷貝
    for (const [key, value] of Object.entries(obj)) {
        result[key] = deepClone(value, map);
    }

    // 最後回傳結果
    return result;
}


const originalData = {
    undefined: undefined, 
    notANumber: NaN, 
    infinity: Infinity, 
    regExp: /.*/, 
    date: new Date('1999-12-31T23:59:59'), 
	
    firstLayerNum: 10,
    obj: {
        secondLayerNum: 100,
    },
};
const clonedData = deepClone(originalData);

clonedData.firstLayerNum = 20;
clonedData.obj.secondLayerNum = 200;

console.log(originalData.undefined);
console.log(originalData.notANumber);
console.log(originalData.infinity);
console.log(originalData.regExp);
console.log(originalData.date);
console.log('-------------------');
console.log(clonedData.undefined); //undefined type:undefined
console.log(clonedData.notANumber); //NaN  type:number
console.log(clonedData.infinity); //Infinity null type:number
console.log(clonedData.regExp); // /.*/ type:object
console.log(clonedData.date); // 1999-12... type:object

console.log(originalData.firstLayerNum);
// 10 => 第一層「沒有」被 clonedData 影響
console.log(originalData.obj.secondLayerNum);
// 100 => 第二層「沒有」被 clonedData 影響
```



