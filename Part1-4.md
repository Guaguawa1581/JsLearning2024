### Ch.4 閉包
#### 4.1 閉包的特性
閉包就是 Function 內的 Function
閉包內層的變數記憶體不會被釋放，不會被外面直接讀取, 可以被內層函式操作(透過return傳遞操作後的值)
```javascript
function outer() {
    var a = 0; //無法從外部更改
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

##### 不使用閉包
不使用閉包的情況下，不論執行**countDogs()**或**countCats()**,
都會更動到全域變數count
可能導致變數的汙染
```javascript
// dogHouse
var count = 0;
function countDogs() {
    count += 1;
    console.log(count + ' dog(s)');
}

// catHouse
var count = 0;
function countCats() {
    count += 1;
    console.log(count + ' cat(s)');
}

countCats(); // 1 cat(s)
countCats(); // 2 cat(s)
countCats(); // 3 cat(s)

countDogs(); // 4 dog(s)，希望是 1 dog(s)
countDogs(); // 5 dog(s)，希望是 2 dog(s)

countCats(); // 6 cat(s)，希望是 4 cat(s)
```

##### 使用閉包
```javascript
function dogHouse() {
    var count = 0;
    function countDogs() {
        count += 1;
        console.log(count + " dogs");
    }
    return countDogs; // return出的countDogs執行完的結果
}

const countDogs = dogHouse(); // countDogs執行的位置是在dogHouse()中
countDogs();   // "1 dogs"
countDogs();   // "2 dogs"
```



