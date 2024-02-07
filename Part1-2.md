### Ch.2 詞法作用域
#### 2.1.1 執行順序
![執行順序](https://imgur.com/BDll1hT "執行順序")
Js優先處理處理堆(stack)中**最上面**的 **執行環境(Execution Context)**
執行完畢 該**執行環境(Execution Context)** 就會被移除

#### 2.1.2 執行環境(Execution Context)的構成
執行環境（Execution Context）內有需多物件
1. 全域物件，在有瀏覽器的環境下，指的是window內的物件
2. 登記這個環境所宣告的變數和function(函式)
3. 外部環境，也就是Scope Chain，台灣多半翻譯成「作用域（鏈）」，中國則翻譯成「範疇」
4. 這個環境的”this”是什麼

![ExecutionContext](https://imgur.com/Dk8NMCQ)