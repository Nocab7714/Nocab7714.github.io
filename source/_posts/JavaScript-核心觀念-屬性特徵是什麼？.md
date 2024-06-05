---
title: JavaScript 核心觀念 - 屬性特徵是什麼？
date: 2024-04-28 14:03:47
tags: JavaScript
description: 本篇章節會介紹何謂物件中的屬性特徵，透過認識屬性特徵，可以讓我們更加細部的對物件進行設定與控制。
---

## 前言

本篇章節會介紹何謂物件中的屬性特徵，透過認識屬性特徵，可以讓我們更加細部的對物件進行設定與控制。

---

## 什麼是屬性特徵呢？

在我們平時定義物件之後，如果打開 chrome 的 console 可以看到內容如下圖，我們只能知道物件中的值（value）：

```jsx
  var person = {
    a: 1,
    b: 2,
    c: 3
  }
  
  console.log(person);
```

![console 結果](https://i.imgur.com/3Usl9n0.png)

可是這種方法並不能夠完全的看到物件中的屬性特徵，我們只能夠透過[「Object.getOwnPropertyDescriptor」](<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor> "Object.getOwnPropertyDescriptor() - JavaScript | MDN")這段語法來顯示出物件的屬性特徵：

```jsx
  var person = {
    a: 1,
    b: 2,
    c: 3
  }
  
  console.log(Object.getOwnPropertyDescriptor(person,'a'));
  // Object.getOwnPropertyDescriptor(物件名稱,'屬性名稱')
```

在程式碼運行後的結果就可以看到以下內容：
![Object.getOwnPropertyDescriptor 的 console 結果](https://i.imgur.com/RsGs9ie.png)

從內容中我們就可以看到這四個值就是物件中的其中一個屬性的所有屬性特徵：

```jsx
{
  value: 1,
  writable: true,
  enumerable: true,
  configurable: true,
}
```

那麼這四個屬性值分別是：

- value：屬性值。
- writable : 可否被修改屬性值。
- configurable：可否被刪除。
- enumerable：可否被列舉。（例如是否可以被迴圈讀取出來）

那麼這四個屬性特徵究竟要如何操作呢？接下來我們透過另一段的語法「Object.defineProperty」來介紹這四個屬性特徵吧！

---

## Object.defineProperty 如何使用？

關於 Object.defineProperty，我們可以先參考 [MDN 文件](<https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty> "Object.defineProperty() - JavaScript | MDN")中的介紹認識到他的使用方式，並且透過文件中了解到 Object.defineProperty 這段語法的使用方式如下：

```jsx
    var person = {
    a: 1,
    b: 2,
    c: 3
  }
  
  Object.defineProperty(person,'a',{
    value　: 1 ,
    writable　: true ,
    configurable　: true ,
    enumerable : true 
  }
```

在 Object.defineProperty 的括弧中帶入的參數分別為，第一個是物件名稱；第二個屬性名稱（記得要用單引號包住）；第三個屬性特徵的設定（記得用大括號包住），並且這些屬性特徵中除了 value 的值以外，其他三項的預設值都是 true。

在大致了解屬性特徵是什麼以後，接下來我們就要來嘗試透過這段語法對屬性特徵進行調整。想必 value 這個屬性特徵就如字面上的意思，是調整物件屬性的值，所以這部分的操作並不有太多的介紹，我們直接對「writable」這個屬性特徵進行調整。

### 調整 writable

我們延續上一個的物件範例：

```jsx
  var person = {
    a: 1,
    b: 2,
    c: 3
  }

  console.log('修改前: ', person);
```

接著我們使用 Object.defineProperty 來修改物件中的值。設定 person 物件中 a 屬性的值為 5 ，並且設定無法修改屬性值：

```jsx
  // 修改屬性值，並設定值無法被複寫
  Object.defineProperty(person,'a',{
    value: 5,
    writable: false,
    configurable: true,
    enumerable: true,
  })

  console.log('修改後: ', person);

  person.a = 10;
  console.log('嘗試複寫: ', person);
```

![ 使用 Object.defineProperty 修改 writable 屬性特徵的結果](https://i.imgur.com/iSGR3PY.png)

>透過 Object.defineProperty 修改屬性中的值可以看到，第一次的設定 a 屬性的 value 值為 5 有成功修改，不過因為同時將 writable 設定為 false，後續透過點記法 person.a 嘗試複寫修改 a 屬性的值為 10 的時候，結果並未成功的被修改。

{% note warning %}
這邊要注意一下，因為是「靜默錯誤」的關係，這邊嘗試修改後執行程式碼並不會跳出錯誤訊息，所以在除錯上可能會造成困難。如果希望能夠跳出錯誤的訊息，這時候可以使用嚴謹模式「use strict」。
{% endnote %}

使用 use strict 的模式觀察：

```jsx
  (function(){
    'use strict';
    person.a = 5;
  })();
```

![use strict 的模式觀察的結果](https://i.imgur.com/7JYFE2Y.png)
>由於 writable 設定為 false，在嘗試修改物件的屬性值時會跳出錯誤訊息 ncaught TypeError: Cannot assign to read only property 'a' of object '#Object’ ，代表該物件設定為無法修改複寫屬性值。

在嚴謹模式下的 console 結果就可以看到系統跳出的錯誤訊息「Uncaught TypeError: Cannot assign to read only property 'a' of object '#Object’」，這樣就可以提醒其他使用者在操作這段程式碼的時候，能夠知道物件的屬性特徵是設定為無法被複寫修改的。

### 調整 configurable

那麼在上方示範的是調整 value 與 writable 的設定，接下來我們換嘗試設定「configurable」的屬性特徵：

```jsx
  var person = {
    a: 1,
    b: 2,
    c: 3
  }

  Object.defineProperty(person, 'b', {
    configurable : false // configurable 設定為 false ,代表無法被刪除
  })

  delete person.a;
  delete person.b;
  console.log(person);
```

在屬性特徵的部分若沒有要調整，可以不用手動設定，就依照原本的預設值，所以下方的範例程式碼中，我們只要修改 person 物件中的 b 屬性 ，將 configurable 的值設定為 false。在設定 configurable 為 false 後，表示接下來 person 的 b 屬性是無法被刪除的，我們可以嘗試使用 delete 的運算子刪除屬性 a 與 b，在結果可以看到，a 屬性被成功刪除，不過 b 屬性是依照 configurable 為 false 的設定而無法被刪除。

![使用 Object.defineProperty 修改 configurable 屬性特徵的結果](https://i.imgur.com/QHqRigw.png)

>在嘗試刪除物件中的 a、b 屬性，由於 b 屬性特徵設定為無法刪除，所以 b 屬性在使用 delete 的操作符後，該屬性值依然保留了下來。

### 調整 enumerable

最後一個我們來調整看看屬性特徵「enumerable」為 false 會發生什麼事情：

enumerable 為列舉，我們設定 person 物件的 c 屬性特徵 enumerable 為 false，代表 c 屬性接下來無法被列舉。你說什麼是列舉呢？我們可以透過 for 迴圈來觀察，將 person 物件帶入 for 迴圈中，正常情況下是物件中每個屬性都會被列舉出來，不過由於使用 Object.defineProperty 設定物件中的 c 屬性為無法被列舉的，也就是 enumerable 為 false，所以在最後呈現的 console 結果並不會顯示 c 屬性值，只有 a 和 b 。

```jsx
  var person = {
    a: 1,
    b: 2,
    c: 3
  }

  Object.defineProperty(person,'c',{
    enumerable : false // enumerable 設定為 false ,代表無法被列舉
  })

  for( var key in person){
    console.log('列舉: ' + key);
  }
```

![使用 Object.defineProperty 修改 enumerable 屬性特徵的結果](https://i.imgur.com/FkWqUdK.png)

以上就是關於使用 Object.defineProperty 這段語法來操作屬性特徵的示範。

## 關於屬性的淺層保護

在 Object.defineProperty 針對屬性特徵 value 的設定僅有做到淺層保護，如下方的範例：

```jsx
  var person = {
    a: 1,
    b: 2,
    c: 3
  }

  Object.defineProperty(person,'d',{
    value:{},
    writable: false,
  })

  person.d = 6;
  console.log('直接賦予值', person);
```

一開始我們先在 person 物件中使用 Object.defineProperty 設定一個新的屬性 d，該屬性的 value 為一個空物件，並且設定 writable 為 false 使資料無法被複寫。在這之後我們使用點記號的方式賦予值給 d 屬性，可以看到在後面 console 的結果會因為 writable 設定為 false，所以 person.d = 6; 這段程式碼並沒有成功將值給複寫。

![淺層保護範例](https://i.imgur.com/wtZpGPH.png)

>d 屬性由於 writable 設定為 false，所以依舊保持原有的空物件的值。

不過如果我們將賦予值的方式改為這樣：

```jsx
person.d.a = 10;
```

![淺層保護範例](https://i.imgur.com/kpljjtW.png)

>在結果上就可以看到 d 屬性當中原本的空物件被覆寫了，改為 a 屬性 10 的值。這是因為 Object.defineProperty 針對 value 的設定僅有做到淺層保護。

---

## 一次只能設定一個屬性的屬性特徵嗎？

使用「Object.defineProperties」就可以大量的修改物件中的屬性特徵。使用的方式可以參造 [MDN 文件](<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties> "Object.defineProperties() - JavaScript | MDN")提供的方式。

使用的方式和 Object.defineProperty 差不多，第一個參數是物件名稱，接著後面放入要修改的屬性，屬性要使用大括弧包裹住，並且物件名稱、屬性特徵皆不用加上 '' 符號，最後用相同的方式設定要修改的屬性特徵：

```jsx
  var person = {
    a: 1,
    b: 2,
    c: 3
  }

  Object.defineProperties(person,{
    a:{
      value:4,
      writable:false,
      enumerable: false,
      configurable: false,
    },
    b:{
      value:5,
      writable:false
    },
    c:{
      value:6,
    },
    d:{
      value:7,
    }
  })

  console.log(person);
```

![Object.defineProperties 範例結果](https://i.imgur.com/BdORVR5.png)
