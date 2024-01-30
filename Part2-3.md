### Ch.3 對象
this根據函數調用位置的不同，會造成this綁定的對象不同，而這裡的對象又是什麼?
#### 3.1 對象與資料類型
##### 語言類型(基本類型)
- string
- number
- boolean
- null
- undefined
- object
**簡單基本類型並不是對象**
**typeof null會返回"object"，此為bug，實際上null本身是基本類型**

##### 內置對象(內建物件)
- String
- Number
- Boolean
- Object
- Function
- Array
- Date
- RegExp
- Error

原理是這樣的，不同的物件在底層都表示為二進制，在 JavaScript 中二進位前三位都為 0 的話會被判
斷為 object 類型，null 的二進位表示是全 0，自然前三位也是 0，所以執行 typeof 時會傳回“object” 

ex:
```javascript
var strPrimitive = "I am a string"; // 字面量
console.log(typeof strPrimitive);  // "string"
console.log(strPrimitive instanceof String); // false

var strObject = new String("I am a string"); // 內置對象
console.log(typeof strObject); // "object"
console.log(strObject instanceof String); // true 
```
原始值 "I am a string" 並不是一個對象，它只是一個字面量，並且是一個不可變的值。
如果要在這個字面量上執行一些操作，例如取得長度、存取其中某個字元等，那需要將其轉換為 String 對象

ex2:
```javascript
var strPrimitive = "I am a string"; 
console.log( strPrimitive.length ); // 13
console.log( strPrimitive.charAt( 3 ) ); // "m"
```
引擎自動把字面量轉換成String對象





------------

#### 深複製&淺複製

#### 屬性描述符
可以更改屬性的特性
writable: false,
静默失败（silently failed）

configurable: false, // 不可配置！
後面再更改設定或delete，會TypeError

關於屬性設定的各項
writable: true,
configurable: true,
enumerable: true

writable 决定是否可以修改属性的值。
configurable 決定之後是否開放屬性修改
只要属性是可配置的，就可以使用 defineProperty(..) 方法来修改属性描述符：

禁止擴展
Object.preventExtensions( myObject )
Object.seal() 調用Object.preventExtensions，密封之後不僅不能添加新屬性，也不能重新配置或刪除任何現有屬性（雖然可以修改屬性的值）
Object.freeze()調用Object.seal

#### Getter和Setter

存在性
Object.hasOwnProperty
```javascript
var myObject = {
    a: undefined
};
console.log(myObject.a);  // undefined
console.log(myObject.b);  // undefined
console.log(myObject.hasOwnProperty("a"));  // true
console.log(myObject.hasOwnProperty("b"));  // false
```

#### 枚舉

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
    // 让 b 不可枚举
    { enumerable: false, value: 3 }
);
console.log(myObject.b); // 3
console.log("b" in myObject); // true
console.log(myObject.hasOwnProperty("b")); // true

for (var k in myObject) {
    console.log(k, myObject[k]);
} // "a" 2 沒有"b"
```

區分屬性是否可以枚舉
Object.propertyIsEnumerable( "a" )
Object.keys() 和 Object.getOwnPropertyNames(..) 都只会查找对象直接包含的属性。








```javascript

var randoms = {
    [Symbol.iterator]: function () {
        return {
            next: function () {
                return { value: Math.random() };
            }
        };
    }
};
var randoms_pool = [];
for (var n of randoms) {
    randoms_pool.push(n);
    // 防止无限运行！
    if (randoms_pool.length === 100) break;
}
```



在 JavaScript 中宣告一個 Object （物件）時使用首尾大括號建立範圍， Object 內的 Property （特性）為一個 key （鍵）搭配一個 value （值），並以逗號區隔每個 Property：

```javascript
//宣告一個 Object 物件，搭配首尾大括號
let mary = {
    //每個 Property 間使用逗號區隔
    name: 'Mary',
    sayHello: function(){
        console.log(`Hello ${this.name}`)
    },
}
```







對象與類型
淺複製與深複製

*原理是這樣的，不同的物件在底層都表示為二進制，在 JavaScript 中二進位前三位都為 0 的話會被判
斷為 object 類型，null 的二進位表示是全 0，自然前三位也是 0，所以執行 typeof 時會傳回“object”

