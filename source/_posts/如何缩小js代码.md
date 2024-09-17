---
title: 如何缩小js代码
date: 2023-09-12 07:05:39
categories: js
---

## 写在前面
相较于简写形式带来的代码体积减小，易于理解的代码书写方式能显著提升可维护性，因此在选择简写方案时需谨慎。

### Arguments

```jsx
setInterval(function () {
  console.log("z");
}, 100); // before
setInterval('console.log("z")', 100); // after
```

### Variables

```jsx
var o = {}; // Object literal
var a = []; // New Array
var r = /.*/; // New Regex
var s = "" + 0; // Convert to string
var n = +"7"; // Convert to number (7)
var b = !!b; // Converts to a boolean
var f = ~~3.434; // Same as Math.floor(3.434)
var g = -~3.434; // Same as Math.ceil(3.434)
var x = 5e3; // Same as 5000
var c = c || z; // Coalesce, if c is null then set it to z.
"abcde"[1]; // charAt shorthand, results in 'b'.
+new Date(); // Shorthand for (new Date()).getTime();
Date.now(); // Even shorter shorthand for the above
var a = x ? y : z; // Ternary operator, short for: var a;if(x){a=y;}else{a=z;}
!0; // Shorthand for true
!1; // Shorthand for false
void 0; // Shorthand for undefined
```

#### 隐式转换

```jsx
a = "30";
b = "10";
c = a + b; // failure
c = parseInt(a) + parseInt(b); // too long

c = -(-a - b); // try these
c = ~~a + ~~b;
c = +a + +b;
c = a - -b;
```

### 运算符

```jsx
hasAnF = "This sentence has an f.".indexOf("f") >= 0; // before
hasAnF = ~"This sentence has an f.".indexOf("f"); // after
// ==================================

// Longhand
if (str.indexOf(ndx) == -1) {
  // Char not found
}

// Shorthand
if (~str.indexOf(ndx)) {
  // Char not found.
}
//===========================================

rand10 = Math.floor(Math.random() * 10); // before
rand10 = 0 | (Math.random() * 10); // after
// ~ 优先级低于|

//===========================================
Math.floor(a / 2); // before
a >> 1; // after

Math.floor(a / 4); // before
a >> 2; // after
//==========================================

million = 1000000; // before
million = (1e6)[ // after
  //==========================================

  (Infinity, -Infinity)
][(1 / 0, -1 / 0)]; // before // after
//=============================================

if (isFinite(a))
  if (1 / a)
    // before
    // after
    //==========================================

    //使用当前日期生成随机整数
    new Date() & 1; // Equivalent to Math.random()<0.5
new Date() % 1337; // Equivalent to Math.floor(Math.random()*1337))

i = 0 | (Math.random() * 100); // before
i = new Date() % 100; // after
//=============================================
```

### string

```jsx
html = "<a href='" + url + "'>" + text + "</a>"; // before
html = text.link(url); // after
//'abc'.link('www.baidu.com') -> '<a href="www.baidu.com">abc</a>'
//==================================================

// Longhand
"myString".charAt(0);

// Shorthand
"myString"[0]; // returns 'm'

//Pretty useful for RGB declarations.
"rgb(" + (x + 8) + "," + (y - 20) + "," + z + ")"; // before
"rgb(" + [x + 8, y - 20, z] + ")"; // after

"rgb(255," + (y - 20) + ",0)"; // before
"rgb(255," + [y - 20, "0)"]; // after
```

### 条件语句

```jsx
// Longhand
var big;

if (x > 10) {
  big = true;
} else {
  big = false;
}

// Shorthand
var big = (x > 10) ? true : false;

var big = (x > 10);

//further nested examples
var x = 3,
big = (x > 10) ? 'greater 10' : (x < 5) ? 'less 5' : 'between 5 and 10';

console.log(big); // "less 5"

var x = 20,
big = { true: x > 10, false : x< =10 };

console.log(big); // "Object {true=true, false=false}"
//==================================================

// Longhand
if(myvar == 1 || myvar == 5 || myvar == 7 || myvar == 22)  {
  console.log('ya');
}

// Shorthand
([1,5,7,22].indexOf(myvar) !=- 1) && alert('yeah baby!');
//=====================================================

// Longhand
if (type === 'aligator') {
  aligatorBehavior();
}
else if (type === 'parrot') {
  parrotBehavior();
}
else if (type === 'dolphin') {
  dolphinBehavior();
}
else if (type === 'bulldog') {
  bulldogBehavior();
} else {
  throw new Error('Invalid animal ' + type);
}

// Shorthand
var types = {
  aligator: aligatorBehavior,
  parrot: parrotBehavior,
  dolphin: dolphinBehavior,
  bulldog: bulldogBehavior
};

var func = types[type];
(!func) && throw new Error('Invalid animal ' + type); func();
//========================================================

//Longhand
if (x == a) {
 x = b;
}
else if (x == b) {
 x = a;
}
// x = 1, a = 1, b = 2
// 1st run: x = 2
// 2nd run: x = 1
// 3rd run: x = 2
// 4th run: x = 1
// ...

// Shorthand
x = a ^ b ^ x;
//====================================================
```
