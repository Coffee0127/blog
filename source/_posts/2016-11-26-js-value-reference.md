---
title: 畫圖理解 JavaScript value reference
categories: JavaScript
toc: false
date: 2016-11-26 23:49:27
tags:
    - JavaScript
    - Must-Know
---
這篇是上完[保哥](https://www.facebook.com/will.fans)的《JavaScript 開發實戰：核心概念篇》筆記之一 ~~(狀態顯示為拖稿)~~

開始之前可以先看看 Stack Overflow 上這篇 [Is JavaScript a pass-by-reference or pass-by-value language?](http://stackoverflow.com/questions/518000/is-javascript-a-pass-by-reference-or-pass-by-value-language)

看完程式碼好想有點感覺，但真相是仍舊什麼都不懂 (￣□￣|||)a

以下就用保哥嫡傳畫圖理解來做說明 ㄟ(￣▽￣ㄟ)

---
#### “物件是記憶體中的資料，跟變數關聯，並在執行時期擁有型別”

開始之前，先準備紙筆繪出以下樣板

{% img inline /2016/11/26/js-value-reference/layout.jpg %}

大致可分為兩區塊：
1. 程式碼區塊：就是用來寫 JavaScript 程式碼的...XD
2. 變數、記憶體空間、型別：說明“物件是**記憶體中的資料**，跟**變數**關聯，並在執行時期擁有**型別**”

---
在以下程式碼片段執行的過程中，出現過幾個物件？幾個變數？幾種型別？
```js
var a;
a = 1;
a = "a";
a = "a" + a;
```
接下來就將程式碼一步一步寫下，並繪製對應圖形～

1. 在程式碼區塊寫上 `var a`，並於變數畫上 a，因為沒給值因此是 `undefined`，那 `undefined` 的型別叫 `undefined`
{% img inline /2016/11/26/js-value-reference/ex1-1.jpg %}

2. 將 1 assign 給 a，因此記憶體內會產生型別為 `number` 的 `1`，並且變數 `a` 指向 `1`
{% img inline /2016/11/26/js-value-reference/ex1-2.jpg %}

3. 將字串 a assign 給 a，因此記憶體內會產生型別為 `string` 的 `a`，並且變數 `a` 指向 `a`；而原本的 `1` 則會被 GC (Garbage Collection)，因此改為虛線
{% img inline /2016/11/26/js-value-reference/ex1-3.jpg %}

4. 等號右邊先執行，因此記憶體內會再產生另一型別為 `string` 的 `a` (JavaScript 沒有所謂字串池觀念，因此會產生新的字串)，並且跟原本變數 `a` 指向的字串 `a` 相加後產生新的字串 `aa`
{% img inline /2016/11/26/js-value-reference/ex1-4.jpg %}

5. 並將新產生的字串 `aa` assign 給 a (原本變數 `a` 指向的字串 `a` 及新產生的字串 `a` 都會被 GC)
{% img inline /2016/11/26/js-value-reference/ex1-5.jpg %}

6. 最後整理一下圖形
{% img inline /2016/11/26/js-value-reference/ex1-6.jpg %}
計算一下即可得知程式碼執行過程中，一共產生了 5 個物件、1 個變數、3 個型別 <( ‵▽′)b good job

**請注意 `undefined` 並不會被 GC！** 因為永遠有個變數指向 `undefined`，即 `window.undefined`

---
接下來看看**物件**的圖形又應該怎麼畫

```js
window.myKey = 1;
window.myKey = 2;
```

1. 如法炮製先寫上程式碼 `window.myKey = 1` 以及畫上變數 `window`，這邊比較不一樣的是在記憶體空間內除了用物件的表示法外，值的部分是另外指向記憶體空間的資料
{% img inline /2016/11/26/js-value-reference/ex2-1.jpg %}

2. 將 2 assign 給 myKey，因此記憶體內會產生型別為 `number` 的 `2`，並且變數 `myKey` 指向 `2`；而原本的 `1` 則會被 GC
{% img inline /2016/11/26/js-value-reference/ex2-2.jpg %}

3. 最後整理一下圖形
{% img inline /2016/11/26/js-value-reference/ex2-3.jpg %}

---
看完以上兩個範例，應該稍微了解圖形應該怎麼畫，再來就是要多練習囉～

```js
var a = { a1: 1, a2: 2 };
var b = [];
b.push(a);
b.push(a);
b[0]['a1'] = 2;
// b[1]['a1'] = ???
```

1. 畫上變數 `a` 、指向的物件及物件的值
{% img inline /2016/11/26/js-value-reference/ex3-1.jpg %}

2. 畫上變數 `b` 及指向的陣列 (注意到 `Array` 的型別仍舊是 `object` )
{% img inline /2016/11/26/js-value-reference/ex3-2.jpg %}

3. 在陣列 `b` 加入兩次 `a`
{% img inline /2016/11/26/js-value-reference/ex3-3.jpg %}

4. 將陣列 `b` 第一個元素的 `a1` 的值指向 `2`；而原本的 `1` 則會被 GC
{% img inline /2016/11/26/js-value-reference/ex3-4.jpg %}

5. 最後整理一下圖形，即可得知 `b[1]['a1']` 是指向 `2`
{% img inline /2016/11/26/js-value-reference/ex3-5.jpg %}

---
更多的練習題～

* Example 1
```js
var a = { a1: 1, a2: 2 };
var b = [];
b.push(a);
b[0]['a1'] = 2;
// a['a1'] = ???
```
    {% img inline /2016/11/26/js-value-reference/ex4-1.jpg %}

* Example 2
```js
var a = { x: 1 };
var b = a;
a = { x: 2 };
// b.x = ???
```
    {% img inline /2016/11/26/js-value-reference/ex5-1.jpg %}
    1. 變數 `a` 指向新的物件 `{ x: 2 }`，因此不影響變數 `b.x`
    2. 因為變數 `b` 仍指向物件 `{ x: 1 }` ，因此物件 `{ x: 1 }` 不會被 GC

* Example 3
```js
var a = { a1: 1, a2: 2 };
function test(p) {
  p.a1 = 2;
}
test(a);
// a.a1 = ???
```
    {% img inline /2016/11/26/js-value-reference/ex6-1.jpg %}
    1. 變數 `p` 因為是宣告在 `function` 內，因此當 `function` 執行完畢會被銷毀

* Example 4
```js
var a = 1;
function test(p) {
  p = 2;
}
test(a);
// a = ???
```
    {% img inline /2016/11/26/js-value-reference/ex7-1.jpg %}
    1. 變數 `p` 指向的 `1` 跟原本變數 `a` 指向的 `1` 是不同的
    2. 在 `function` 內產生的 `1` 和 `2` 會隨著 `function` 執行完畢而被 GC
    3. 變數 `p` 因為是宣告在 `function` 內，因此當 `function` 執行完畢會被銷毀

---
進階題，理解同時 assign (如 [jQuery](https://github.com/jquery/jquery/blob/1.6.2/jquery.js) 的 `window.jQuery = window.$ = jQuery;` )

* Example 5
```js
var a = { x: 1 };
var b = a;
a.x = a = { x: 2 };
// b.x = ???
// a.x = ???
```

1. 畫上變數 `a` 、指向的物件及物件的值
{% img inline /2016/11/26/js-value-reference/ex8-1.jpg %}

2. 畫上變數 `b` ，並指向變數 `a` **指向的物件**
{% img inline /2016/11/26/js-value-reference/ex8-2.jpg %}

3. 將【變數 `a`】及【變數 `a` 指向的物件 `{ x: 1}` 的變數 `x`】同時指向新的物件 `{ x: 2 }`；而原本的 `1` 則會被 GC
{% img inline /2016/11/26/js-value-reference/ex8-3.jpg %}

4. 最後整理一下圖形，即可得知 `b.x` 指向 `{ x: 2 }`，而 `a.x` 則是指向 `2`
{% img inline /2016/11/26/js-value-reference/ex8-4.jpg %}

所謂 "一圖抵萬言"，透過圖形理解 JavaScript value reference，可以避免產生很多淺在性的 BUG (即當你想的跟執行的不一致 😂)