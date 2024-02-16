### Ch.2 詞法作用域
#### 2.1 執行順序
![執行順序](https://imgur.com/3n7aYvQ.gif "執行順序")
Js優先處理處理堆(stack)中**最上面**的 **執行環境(Execution Context)**
執行完畢 該**執行環境(Execution Context)** 就會被移除

#### 2.2 執行環境(Execution Context)的構成
執行環境（Execution Context）內有需多物件
1. 全域物件紀錄，在有瀏覽器的環境下，指的是window內的物件
2. 環境變數宣告紀錄表, 登記這個環境所宣告的變數和function(函式)
3. 外部環境，也就是Scope Chain，作用域(鏈)
4. 這個環境的”this”是什麼

![ExecutionContext](https://i.imgur.com/4sGWxHZ.png)

#### 2.3 執行環境的 環境變數宣告記錄表
全域執行環境會把變數登記在 **"環境變數宣告紀錄表（Declarative Environment Record）"** 中

##### 全域
這個範例中，**全域執行環境**的**宣告記錄表**中，登記了 "a", "f1", "f3" 三個變數。
外部環境outerEnv，則標記「需要存取變數時，除了自己的宣告記錄表，我還可以在哪裡找到變數？」
由於全域執行環境是第一個執行環境，因此外部環境為null。
![globalEC](https://i.ibb.co/C9zCm16/EC.png)

##### f1
在f1()執行環境的宣告記錄表中，登記了b、f2兩個變數。
![f1EC](https://i.imgur.com/nMIwMyp.png "f1EC")
變數的尋找順序
![f1ECO](https://i.imgur.com/DHLvcTr.png "f1ECO")

##### f2
在f2()執行環境的宣告記錄表中，登記變數c
外部環境則註記了它的上一層f1()執行環境
![f2EC](https://i.imgur.com/dgAyMzp.png "f2EC")
變數的尋找順序
1. 在自己的宣告記錄表裡面找，找不到看下一步
2. 透過自己的outerEnv，去**f1EC.lexicalEnv.DeclEnvRec**裡面找，找不到看下一步
3. 透過f1.lexicalEnv.outerEnv，去**globalEC.lexicalEnv.DeclEnvRec**裡面找。由於這是最後的lexicalEnv了，找不到的話會拋出ReferenceError。

![f2ECO](https://i.imgur.com/c7UAXtQ.png "f2ECO")

##### f3
在f3()執行環境的宣告記錄表中，登記變數d
![f3EC](https://i.imgur.com/hIODJv9.png "f3EC")
變數的尋找順序
![f2ECO](https://i.imgur.com/d0aiYp4.png "f3ECO")

#### 2.4 執行環境的 作用域
**作用域**可以是為環境中變數有效的範圍
範例中的globalEC最大
**作用域鏈** 則指，當下DER外，還有哪裡可以找變數
![allEC](https://i.imgur.com/GEstJg5.png "allEC")

#### 2.5 詞法作用域
沒有上下關係的函數的作用域鏈是獨立的，如**f2()**跟**f3()**,
**f2()** 的作用域鏈是 f2EC.VO + f1EC.VO + globalEC.VO
**f3()** 的作用域鏈是 f3EC.VO + globalEC.VO
JS中作用域的決定在程式運行之前的"語法解析"階段，這種就稱為 **靜態作用域**==**詞法作用域**

#### 2.6 一般作用域的問題
function是作用域的分界，但與Function無關的括號會影響對變數的判斷，
範例中在if判斷式中的var flag會影響到If{}外的值
**這樣很不直覺**
```javascript
function doSomething(condition) {
     if (condition) {
        var flag = 2;
        console.log(flag); // 2
    }

    console.log(flag); // 2
}

var condition = true;
doSomething(condition);
```

#### 2.7 塊作用域
ES6加入 let, const宣告變數的方式,
其以**大括號{}**作為分界，彌補了作用域不夠有彈性的缺陷
```javascript
function doSomething(condition) {
  let myVar = 1;
  if (condition) {
    let myVar = 2;
    console.log(myVar); // 2
  }
	
  console.log(myVar); // 1
}

var condition = true;
doSomething(condition);
```

##### let/const並不是沒有拉升，而是沒有初始化為undefined
![letconst](https://i.imgur.com/IQ9fa0O.png)
