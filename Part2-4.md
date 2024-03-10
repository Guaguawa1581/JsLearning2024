# Javascript 的 Class

### Class-Based 語言中的 Class

這是 Java 的 Class

1. 分成 Class 和 Object 兩種，**兩者不一樣**。
1. Class 內部會描述 Properties 和 Method。
1. Class 能建構出 Object ，也被稱作 Instance 。
1. Instance 能夠使用 Class 內的 Method，並藉由 Method 存取 Object 內的資料。

### Class-Based 語言 與 Js(Prototype-Based)的差別

JS 是 **Prototype-Based** 基於原型的程式語言，相對於**Class-Based**基於類的語言
兩者的差別在於:

- Class-Based 擁有 class 才能產生出對應的 Instance(實例)

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

- Js(Prototype-Based)可以透過直接操作 Prototype 建立不同的 Instance

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
js 的 class 只是用 Class 包裝的 Prototype-Based
:::

---

#### <span style="color:red">★ 後面提到的 class 都是指 Javascript 的 class ★</span>

## class 的用途

假設把 function Animal() 當作設計藍圖
**new** Animal() 就是調用該設計藍圖，返回一個根據構造函式生成的對象

在根據藍圖實例化的時候，使用 class，可以在 Prototype-Based 的基礎上，更容易更直觀地編寫物件導向風格的物件和繼承

- 不使用 class 語法繼承並實例化新物件

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function () {
  //增加Animal的methods
  console.log(`${this.name} makes a noise.`);
};

// 新建Dog，目的繼承Animal
function Dog(name) {
  Animal.call(this, name); // 繼承Animal的屬性
}

Dog.prototype = Object.create(Animal.prototype); // 繼承Animal的方法
Dog.prototype.constructor = Dog; // 修正constructor指向

const dog = new Dog("Rex");
dog.speak(); // 輸出: Rex makes a noise.
```

- 使用 class 語法繼承並實例化新物件 簡潔明瞭

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

constructor 方法是類的默認方法，通過 new 命令生成對象實例時，自動調用該方法。
一個類必須有 constructor 方法，如果沒有顯式定義，一個空的 constructor 方法會被默認添加。

```javascript=
class Point { }
console.log(Point.prototype); // {constructor: ƒ}
```

## 繼承 (extends)

class 使用 extends 繼承這個機制允許一個 class(子類)獲得另一個 class(父類)的屬性及方法。
繼承的好處：

- 代碼重用：子類繼承父類的屬性和方法，無需重新編寫相同的代碼
- 擴展性：可以在子類中添加新的屬性和方法，或者覆蓋（override）父類中的方法。
- 多態性：繼承是實現多態性（Polymorphism）的基礎。通過繼承，不同的子類對象可以被視為是同一父類的實例，但每個子類又可以有其獨特的行為實現。

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

- 代碼重用：Bird 和 Dog 都繼承了 Animal 的**name**屬性和**move()**，避免了重複編寫這部分代碼。
- 擴展性：複寫**move()**呈現不同於父層的結果
- 多態性：通過 class Animal 的繼承，每個動物類型在執行 **move()** 這個 <span style="color:#b50303">"相同的接口"</span> 時會根據其實際內容表現出不同的行動（Bird 會飛，Dog 會跑）。

### super()

"super"關鍵字用於從子類中調用父類的構造函數、方法、或訪問父類的屬性
"super"可以兩種情境下發揮作用

1. 在子類的構造函數中調用父類的構造函數

```javascript=
class Parent {
  constructor(name) {
    this.name = name;
  }
  intro() {
    console.log(`I'm ${this.name}`);
  }
}

class Child extends Parent {
  constructor(name, age) {
    super(name);
    this.age = age;
  }
  intro() {
    console.log(`I'm ${this.name}.  ${this.age} years old.`);
  }
}

const b = new Child("Bob", 19);
b.intro(); // I'm Bob.  19 years old.
```

★ 在子類構造函式沒使用 super 會報錯
子類訪問 this 之前，必須先調用父類別的構造函數

```javascript=
class Parent {
  constructor(name) {
    this.name = name;
  }
  intro() {
    console.log(`I'm ${this.name}`);
  }
}

class Child extends Parent {
  constructor(name, age) {
    // super(name); // 忘記調用super會導致錯誤
    this.age = age;
    // ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
  }
  intro() {
    console.log(`I'm ${this.name}.  ${this.age} years old.`);
  }
}

```

2. 在子類中調用父類的方法

```javascript=
class Parent {
  constructor(name) {
    this.name = name;
  }
  run() {
    console.log(`I'm running. ${this.name}`);
  }
}

class Child extends Parent {
  constructor(name, age) {
    super("wowowo");
    this.name = name;
    this.age = age;
  }
  daily() {
    super.run();
    console.log("And make lunch.");
  }
}

const b = new Child("Bob", 19);
b.daily();
// I'm running. Bob
// And make lunch.
```

## 多型(Polymorphism)

繼承是多型能夠實現的必要條件，在物件導向設計中，多型是一個概念，其作用是讓多個不同的類能夠使用**相同的接口（interface ）**來操作。具體可以在父層提供**介面**或**抽象類別**來實現，不會發生錯誤。
![218912](https://hackmd.io/_uploads/BJpmHbjpT.jpg)
實例(a1, a2, b1,b2)和繼承 Foo 的子類 Bar，箭頭表示複製
子類 Bar 可以通過多型來使用父類定義的 methods，且該 method 是一個副本，並複寫不會影響父類原先定義的內容
**ex:**

```javascript=
// 定義一個父類別 Animal，它有一個方法 speak
class Animal {
  speak() {
    console.log("動物叫");
  }
}
// 定義一個子類別 Dog，繼承自 Animal，相同接口並複寫Overriding
class Dog extends Animal {
  speak() {
    console.log("小狗汪");
  }
}
// 定義一個子類別 Cat，繼承自 Animal
class Cat extends Animal {
  speak() {
    console.log("小貓喵");
  }
}

// 使用多型
function makeAnimalSpeak(animal) {
  // 這個函數接受一個 Animal 類型的對象, 可以用型別判斷過濾
  // 並調用它們統一的接口 speak
  animal.speak();
}
// 實例不同類型的對象
const dog = new Dog();
const cat = new Cat();

// 產生不同結果
makeAnimalSpeak(dog); // 小狗汪
makeAnimalSpeak(cat); // 小貓喵


```

## 多重繼承

多重繼承指的是一個類別可以同時從多個父類別中繼承屬性和方法的能力
![image](https://hackmd.io/_uploads/B1MEibwp6.png)
D 同時從 B, C 繼承

然而，多重繼承也可能引入一些問題，例如：

1. 名稱衝突（Name Collision）：如果兩個父類別中有相同名稱的方法或屬性，子類別在調用時可能會引發錯誤。
2. 鑽石問題（Diamond Problem）：當一個類別繼承了兩個具有相同父類別的父類別，並且這兩個父類別又有一個共同的父類別時，可能會導致方法衝突。

### 用 JS 上做多重繼承 Mixin

JS 沒有原生的機制做多重繼承，因為實際上 JS 只有物件，沒有 class
混入（Mixin）就是一種模擬 class 複製並實現類似多重繼承效果的行為。
又有兩種類型的混入:顯示跟隱式

#### 顯示混入

直接將一個對象的屬性或方法拷貝到另一個對象上
通常會設定一個函數，這個函數將一個或多個源對象的屬性或方法直接拷貝到目標對象上

```javascript=
// 定義一個 mixin 函數，用於將源對象的屬性和方法拷貝到目標對象
function mixin(sourceObj, targetObj) {
  for (var key in sourceObj) {
    // 只在不存在的情況下複製
    if (!(key in targetObj)) {
      targetObj[key] = sourceObj[key];
    }
  }
  return targetObj;
}
var Vehicle = {
  engines: 1,
  ignition: function () {
    console.log("Turning on my engine.");
  },
  drive: function () {
    this.ignition();
    console.log("Steering and moving forward!");
  }
};

var Car = mixin(Vehicle, {
  wheels: 4,
  drive: function () {
    Vehicle.drive.call(this);
    console.log("Rolling on all " + this.wheels + " wheels!");
  }
});
Car.ignition(); //Turning on my engine.
Car.drive();
// Turning on my engine.
// Steering and moving forward!
// Rolling on all 4 wheels!
```

Car 中的屬性 ignition 只是從 Vehicle 複製過來的對於 ignition() 函數的引用

#### 隱式混入

通過將一個對象設置為另一個對象的原型(prototype)上，間接地共享方法。
通常使用"Object.assign()"或展開運算符"..."在 prototype 上來實現

```javascript=
// 定義兩個基本行為作為混入
const FlyerMixin = {
  fly() {
    console.log(`${this.name} is flying.`);
  }
};

const SwimmerMixin = {
  swim() {
    console.log(`${this.name} is swimming.`);
  }
};

// 定義一個函數來實現將多個object混入到一個Class
function applyMixins(targetCtors, sourceCtors) {
  sourceCtors.forEach((sourceCtor) => {
    Object.getOwnPropertyNames(sourceCtor).forEach((name) => {
      targetCtors.prototype[name] = sourceCtor[name]; // 將baseCtors的屬性分給
    });
  });
}

// 定義一個類別，我們將把混入應用到這個類別
class Duck {
  constructor(name) {
    this.name = name;
  }
}

// 將FlyerMixin和SwimmerMixin混入到Duck類別
applyMixins(Duck, [FlyerMixin, SwimmerMixin]);

// 創建一個Duck實例，並調用混入的方法
const guagua = new Duck("Guagua");
guagua.fly(); // 輸出: Donald is flying.
guagua.swim(); // 輸出: Donald is swimming.
```
