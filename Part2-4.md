# Javascript的class
js是 Prototype-Based 物件導向中，不區分 Class 和 Object 所有的東西都可以是 Object


Class 只是簡化了 JavaScript 中操作 Constructor 的語法糖而已。

# class作用
在JavaScript中，「類」(Classes) 是一種語法糖，用於實現基於原型的繼承和物件導向編程。類提供了一種更清晰、更熟悉的方式來創建物件和實現繼承。在ES6 (ECMAScript 2015) 中被正式引入，目的是為了使得JavaScript的物件導向編程更加容易和直觀。

一個類可以看作是創建物件的藍圖。它定義了物件的屬性和方法。類本身不是物件，但通過使用new關鍵字，你可以根據類的定義創建新的物件。

# class的目的
js透過class語法實現類的功能

# class的實行
假設有個class CoolGuy() 構造函式
new CoolGuy() 的功用就是調用該設計藍圖，返回一個根據構造函式生成的對象
這個動作稱作 
:::info 
"實例" 
:::

# 繼承
# 多型
物件導向程式設計中指的是能夠使用相同的接口（例如方法或屬性）來操作不同類型的對象。
這意味著對象可以以多種形式（或類型）出現，但是可以通過同一個接口進行交互。
```javascript=
class Animal {
  speak() {
    console.log('Some sound');
  }
}

class Dog extends Animal {
  speak() {
    console.log('Woof');
  }
}

class Cat extends Animal {
  speak() {
    console.log('Meow');
  }
}

function makeAnimalSpeak(animal) {
  animal.speak();
}

const dog = new Dog();
const cat = new Cat();

makeAnimalSpeak(dog); // 輸出: Woof
makeAnimalSpeak(cat); // 輸出: Meow

```
# 混入 
將一個或多個類的方法添加到一個類中，不使用繼承
![image](https://hackmd.io/_uploads/B1MEibwp6.png)

* 顯式混入：明確地將一個對象的方法拷貝到另一個對象上。
直接將一個對象的屬性或方法拷貝到另一個對象上
通常使用"Object.assign()"或展開運算符"..."在prototype上來實現
* 隱式混入：通過將一個對象設置為另一個對象的原型，間接地共享方法。
