# Javascript的Class
### Class-Based語言中的Class
這是Java的Class
1. 分成 Class 和 Object 兩種，**兩者不一樣**。
1. Class 內部會描述 Properties 和 Method。
1. Class 能建構出 Object ，也被稱作 Instance 。
1. Instance 能夠使用 Class 內的 Method，並藉由 Method 存取 Object 內的資料。

### Class-Based語言 與 Js(Prototype-Based)的差別
JS是 **Prototype-Based** 基於原型的程式語言，相對於**Class-Based**基於類的語言
兩者的差別在於:

-  Class-Based擁有class才能產生出對應的Instance(實例)

```java=    
// Java中Instance，需要有對應的class
public class Animal {
    String name;

    public Animal(String name) {
        this.name = name;
    }

    public void speak() {
        System.out.println(this.name + " makes a sound.");
    }
}

Animal myAnimal = new Animal("Rex");
myAnimal.speak();
```

-  Js(Prototype-Based)可以透過直接操作Prototype建立不同的Instance

```javascript=
// 修改prototype來實現繼承
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function() {
  console.log(`${this.name} makes a noise.`);
}

function Dog(name) {
  Animal.call(this, name); // 調用Animal構造函數，繼承屬性
}

// 設置Dog的原型為Animal的實例，實現繼承
Dog.prototype = Object.create(Animal.prototype);

// 設置構造器指向，確保instanceof運算符正確工作
Dog.prototype.constructor = Dog;

// 可以為Dog實例添加專有方法
Dog.prototype.speak = function() {
  console.log(`${this.name} barks.`);
}

const dog = new Dog('Rex');
dog.speak(); // Rex barks.

```
:::info
js的class 只是用Class 包裝的 Prototype-Based
:::


--------

#### <span style="color:red">★ 後面提到的class都是指 Javascript的class ★</span>
## class的用途
假設把function Animal() 當作設計藍圖
**new** Animal() 就是調用該設計藍圖，返回一個根據構造函式生成的對象

在根據藍圖實例化的時候，使用class，可以在Prototype-Based的基礎上，更容易更直觀地編寫物件導向風格的物件和繼承

- 不使用class語法繼承並實例化新物件
```javascript
function Animal(name) {
    this.name = name;
}

Animal.prototype.speak = function () { //增加Animal的methods
    console.log(`${this.name} makes a noise.`);
};

// 新建Dog，目的繼承Animal
function Dog(name) {
    Animal.call(this, name); // 繼承Animal的屬性
}

Dog.prototype = Object.create(Animal.prototype); // 繼承Animal的方法
Dog.prototype.constructor = Dog; // 修正constructor指向

const dog = new Dog('Rex');
dog.speak(); // 輸出: Rex makes a noise.
```

- 使用class語法繼承並實例化新物件 簡潔明瞭
```javascript!
// 宣告class
class Animal {
    constructor(name) {
        this.name = name;
    }

    speak() {
        console.log(`${this.name} makes a noise.`);
    }
}
// 新建Dog，繼承Animal
class Dog extends Animal {
    constructor(name) {
        super(name); // 調用父類的constructor
    }
}

const dog = new Dog('Rex');
dog.speak(); // 輸出: Rex makes a noise.

``` 
## coustructor
constructor方法是類的默認方法，通過new命令生成對象實例時，自動調用該方法。
一個類必須有constructor方法，如果沒有顯式定義，一個空的constructor方法會被默認添加。
```javascript=
class Point { }
console.log(Point.prototype); // {constructor: ƒ}
```

## 繼承 extends
繼承這個機制允許一個class(子類)獲得另一個class(父類)的屬性及方法。
繼承的好處：
* 代碼重用：子類繼承父類的屬性和方法，無需重新編寫相同的代碼
* 擴展性：可以在子類中添加新的屬性和方法，或者覆蓋（override）父類中的方法。
* 多態性：繼承是實現多態性（Polymorphism）的基礎。通過繼承，不同的子類對象可以被視為是同一父類的實例，但每個子類又可以有其獨特的行為實現。

**ex**
```javascript=
class Animal {
    constructor(name) {
        this.name = name;
    }
    speak() {
        console.log(`${this.name} 會說話 `);
    }
    move() {
        console.log(`${this.name} 會移動 `);
    }
}

class Bird extends Animal {
    move() {
        console.log(`${this.name} 會飛`);
    }
}

class Dog extends Animal {
    move() {
        console.log(`${this.name} 會跑 `);
    }
}

const bird = new Bird('小鳥');
const dog = new Dog('小狗');

const animals = [bird, dog];

dog.speak(); // 小狗 會說話 
dog.move(); //
bird.move(); // 小鳥 會飛
animals.forEach(animal => {
    animal.move();
});
// 小鳥 會飛
// 小狗 會跑 
```
在這個範例中：

* 代碼重用：Bird和Dog都繼承了Animal的**name**屬性和**move()**，避免了重複編寫這部分代碼。
* 擴展性：複寫**move()**呈現不同於父層的結果
* 多態性：通過class Animal的繼承，每個動物類型在執行 **move()** 這個 <span style="color:#b50303">"相同的接口"</span> 時會根據其實際內容表現出不同的行動（Bird會飛，Dog會跑）。

### super()
"super"是個特殊的語法，用於從子類中調用父類的構造函數、方法、或訪問父類的屬性








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
