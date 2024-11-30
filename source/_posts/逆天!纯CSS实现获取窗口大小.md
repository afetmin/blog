---
title: 逆天!纯CSS实现获取窗口大小
date: 2024-11-29T20:03:39Z
lastmod: 2024-11-29T20:52:10Z
---

# 逆天!纯CSS实现获取窗口大小

先看看效果

<video controls="controls" src="./assets/Video_2024-11-29_201258-20241129202925-emiv1pj.mp4" data-src="./assets/Video_2024-11-29_201258-20241129202925-emiv1pj.mp4"></video>

看看代码是怎么样的

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<style>
    @property --vw {
        syntax: "<length>";
        inherits: true;
        initial-value: 100vw;
    }

    @property --vh {
        syntax: "<length>";
        inherits: true;
        initial-value: 100vh;
    }

    :root {
        --w: tan(atan2(var(--vw), 1px));
        --h: tan(atan2(var(--vh), 1px));
    }

    body::before {
        counter-reset: w var(--w) h var(--h);
        content: counter(w) "X" counter(h);
        font-size: 100px;
        margin: auto;
        height: fit-content;
        width: fit-content;
        position: fixed;
        inset: 0;
    }
</style>
<body>
</body>
</html>
```

就这么几行代码

我们来分析一下

首先，通过伪元素实现内容的水平垂直居中

```css
body::before {
        content: “width X height”;
        font-size: 100px;
        margin: auto;
        height: fit-content;
        width: fit-content;
        position: fixed;
        inset: 0;
}
```

然后定义了两个 CSS 的自定义属性：

```css
@property --vw {
        syntax: "<length>";
        inherits: true;
        initial-value: 100vw;
    }

@property --vh {
        syntax: "<length>";
        inherits: true;
        initial-value: 100vh;
}
```

详细的解释：

1. ​ **​`@property --vw { ... }`​** ​:

   * 这行代码定义了一个新的 CSS 自定义属性 `--vw`​。`@property`​ 是一个 CSS at-rule，用于定义自定义属性。在这个例子中，`--vw`​ 是一个自定义属性，用于表示视口宽度的百分比。
2. ​**​`syntax: "<length>";`​** ​:

   * 这行代码定义了 `--vw`​ 的语法类型。`<length>`​ 表示这个自定义属性的值是一个长度值，例如 `px`​, `em`​, `rem`​, `vw`​, `vh`​ 等。
3. ​**​`inherits: true;`​** ​:

   * 这行代码定义了 `--vw`​ 是否可以继承。`true`​ 表示这个自定义属性可以被子元素继承。
4. ​**​`initial-value: 100vw;`​** ​:

   * 这行代码定义了 `--vw`​ 的初始值。`100vw`​ 表示视口宽度的 100%，即整个视口的宽度。

这几行代码定义了一个名为 `--vw`​ 的 CSS 自定义属性，用于表示视口宽度的百分比。这个属性可以被子元素继承，并且其初始值是视口宽度的 100%。

之后，关键的来了：

```css
:root {
        --w: tan(atan2(var(--vw), 1px));
        --h: tan(atan2(var(--vh), 1px));
}
```

首先我们知道三角形一个角的对边/临边= `tanA`​，用反三角函数可以求得 `A`​ 的角度。也就是说 `atan2(var(--vw), 1px)`​ 这里求得的是（浏览器宽度/1px 高度）所对应的那个角的角度值，然后再对这个角度求 `tan`​ 值，即可得出浏览器的当前宽度。这里求得的值是带 `px`​ 单位的，浏览器会自动转换将原来的 `vw `​ 转为 `px`​。

接下来就需要去掉`px`​把这个数值写入页面，利用的是 css `counter`​ 方法：

```css
// 1. 定义 w 为 var(--w) 的名字
counter-reset: w var(--w) h var(--h);
// 2. 利用 counter(w) 拿到值
content: counter(w) "X" counter(h);
```

以上
