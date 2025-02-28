---
title: 复现 musicforprogramming.net 的音乐可视化效果
date: 2025-02-28 19:51:18
categories: js
---

最近，我尝试复现了 [musicforprogramming.net](https://musicforprogramming.net/latest/) 网站上的音乐可视化效果。这个网站为程序员提供了一个独特的音乐播放体验，伴随着音乐的播放，屏幕上会显示动态的 ASCII 艺术效果。本文将详细介绍如何通过 HTML、CSS 和 JavaScript 来实现这一效果。


## HTML 结构

在 `index.html` 文件中，我们定义了一个简单的网页结构。页面包含一个音频播放器（目前被注释掉了）和四个 `<span>` 元素，用于显示不同颜色的 ASCII 艺术效果。此外，还有一个播放按钮，用于控制音乐的播放和暂停。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>musicforprogramming</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="pad grey pr-0 pb-0 pt-0">
        <span id="fuschia"
            class="fuschia">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><br>
        <span id="orange"
            class="orange">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><br>
        <span id="yellow"
            class="yellow">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><br>
        <span id="green" class="green">________________________________</span><br>
        <br>
        <br>
        <button id="play" class="green hover" disabled:true="false">[play]</button>
        <br>
    </div>
    <script src="index.js"></script>
</body>
</html>
```

## JavaScript 逻辑

`index.js` 文件包含了实现音乐播放和可视化效果的核心逻辑。我们使用 `AudioContext` 和 `AnalyserNode` 来处理音频数据，并通过 `requestAnimationFrame` 实现动态的可视化效果。

```javascript
// 获取页面上的元素
const F = document.getElementById("fuschia"); // 获取紫色区域的元素
const O = document.getElementById("orange"); // 获取橙色区域的元素
const Y = document.getElementById("yellow"); // 获取黄色区域的元素
const G = document.getElementById("green");   // 获取绿色区域的元素
const play = document.getElementById("play"); // 获取播放按钮元素

// 创建音频上下文和音频对象
const audioCtx = new AudioContext(); // 创建音频上下文
const audio = new Audio('./daoxiang.mp3'); // 创建音频对象，加载音频文件

// 为播放按钮添加点击事件监听器
play.addEventListener('click', () => {
    if (!audio.paused) { // 如果音频正在播放
        audio.pause(); // 暂停音频
        play.innerText = "[play]"; // 将按钮文本改为 "[play]"
        return;
    }
    audio.play(); // 播放音频
    play.innerText = "[pause]"; // 将按钮文本改为 "[pause]"
});

// 创建音频分析器
const analyser = audioCtx.createAnalyser(); // 创建音频分析器
analyser.minDecibels = -114; // 设置分析器的最小分贝值
analyser.maxDecibels = -30;  // 设置分析器的最大分贝值
analyser.fftSize = 2048;     // 设置 FFT（快速傅里叶变换）的大小
analyser.smoothingTimeConstant = .666; // 设置平滑时间常数，控制音频数据的平滑程度

// 设置音频属性
audio.type = "audio/mp3"; // 设置音频类型
audio.crossOrigin = "anonymous"; // 设置跨域属性，允许跨域请求
audio.volume = .5; // 设置音频音量
audio.autoplay = true; // 设置音频自动播放

// 当音频可以播放时，执行以下操作
audio.addEventListener('canplaythrough', () => {
    // 创建音频源节点
    const source = audioCtx.createMediaElementSource(audio);
    // 创建增益节点（用于控制音量）
    const gainNode = audioCtx.createGain();
    // 将音频源连接到分析器
    source.connect(analyser);
    // 将音频源连接到增益节点
    source.connect(gainNode);
    // 将增益节点连接到音频上下文的输出（通常是扬声器）
    gainNode.connect(audioCtx.destination);

    // 创建一个数组来存储频率数据
    const dataArray = new Uint8Array(analyser.frequencyBinCount);
    // 调用可视化函数，开始渲染音频可视化效果
    visualize(dataArray);
})

// 可视化函数，用于渲染音频的 ASCII 艺术效果
function visualize(dataArray) {
    // 获取当前频率数据
    analyser.getByteFrequencyData(dataArray);
    // 初始化四个颜色的 ASCII 字符串
    let fuschia = "", orange = "", yellow = "", green = "",
        index = 0, adjustValue = -40, multiplier = 1.08, offset = 1;

    // 遍历频率数据，生成 ASCII 字符
    for (i = 0; i < 32; i++) {
        // 计算当前字符的索引，把当前频率数据映射到字符集范围中
        index = Math.max(dataArray[~~offset + i] + adjustValue >> 3, 0);
        offset *= multiplier; // 调整偏移量
        adjustValue += 1.5;    // 调整值
        multiplier += .01;    // 调整乘数
        // 根据索引从字符集中选择字符
        fuschia += "                       _.-•:*^º'"[index];
        orange += "               _.-•:*^º'        "[index];
        yellow += "       _.-•:*^º'                "[index];
        green += "_.-•:*^º'                       "[index];
    }

    // 将生成的 ASCII 字符显示在页面上
    F.innerText = fuschia;
    O.innerText = orange;
    Y.innerText = yellow;
    G.innerText = green;

    // 使用 requestAnimationFrame 递归调用 visualize 函数，实现动态效果
    requestAnimationFrame(() => visualize(dataArray));
}
```

## 可视化效果

在 `visualize` 函数中，我们通过分析音频的频率数据，动态生成 ASCII 字符并将其显示在页面上。每个 `<span>` 元素对应不同的颜色和字符集，从而在页面上呈现出动态的视觉效果。

## 总结

通过这个项目，我们成功复现了 musicforprogramming.net 的音乐可视化效果。这个项目不仅展示了如何使用 Web Audio API 处理音频数据，还展示了如何通过 JavaScript 和 HTML 实现动态的视觉效果。希望这篇文章对你有所帮助，欢迎在评论区分享你的想法和改进建议。

你可以访问 [GitHub 仓库](https://github.com/afetmin/musicforprogramming) 查看完整的代码和效果展示。

Happy coding! 🎵