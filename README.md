# 网页视频任意倍速秘籍

> 适用：YouTube、B站、慕课、网盘预览……一切用原生 `<video>` 标签播放的网页。

## 一句话原理

倍速按钮（0.5x / 1x / 1.5x / 2x）是网站 UI 的**自我限制**，真正的开关是浏览器的原生 API：HTML `<video>` 元素的 `playbackRate` 属性接受**任意正数**。

DevTools Console 和页面共享同一个 JS 执行环境——你直接改 DOM，就绕过了 UI 层，直达浏览器。网站管得着按钮，管不着你调 API。

## 三步通用流程

**1. F12** 打开开发者工具，切换到 Console 面板。

**2. 拿到 video 元素**（三选一）：

```js
// 懒人版：大多数页面主视频只有一个
const v = document.querySelector('video');

// 精确版：Elements 面板里右键视频 → Copy → Copy selector
const v = document.querySelector('#movie_player > div.html5-video-container > video');

// 暴力版：页面有多个 video（广告、预览）时逐个试
document.querySelectorAll('video')[0].playbackRate = 3;
```

**3. 设置倍速**：

```js
v.playbackRate = 3;  // 任意正数都行：0.5 / 2 / 7 / 16 ……
```

一行流：`document.querySelector('video').playbackRate = 3`

## 一劳永逸：书签小工具（Bookmarklet）

新建一个书签，网址栏粘贴下面这段，以后在任何视频页点一下书签，弹窗输入倍速即生效：

```
javascript:(function(){var v=document.querySelector('video');var s=prompt('倍速（任意正数）:','3');if(v&&s){v.playbackRate=parseFloat(s);}else{alert('没找到 video 元素');}})();
```

## 选择器速查

| 写法 | 含义 |
|---|---|
| `#movie_player` | id 为 movie_player 的元素 |
| `.html5-video-container` | class 为 html5-video-container 的元素 |
| `video` | 标签名为 video 的元素 |
| `A > B` | A 的**直接子元素** B |
| `A B` | A 的**任意后代** B |

用 `>` 而不用空格：YouTube 这类页面的 DOM 里可能藏着多个 video（广告、预览），把路径写死能精确命中正在播放的那一个。

## 踩坑记录

1. **`Uncaught SyntaxError: Invalid or unexpected token`** —— JS 字符串字面量不能直接换行，长选择器必须写在一行（或用反引号 `` ` ``）。
2. **`const x = ...` 返回 `undefined` 是正常的** —— 声明语句本来就没有返回值，不代表失败。
3. **选错 video** —— 有广告/预览多个 video 时，用 `querySelectorAll` 逐个试。
4. **网站重置倍速** —— 少数站会监听并把 `playbackRate` 改回去。对策：`setInterval(() => v.playbackRate = 3, 1000)` 定时改回去（简单粗暴但有效）。
5. **canvas 渲染的播放器** —— 压根不用 `<video>` 标签，这招失效。

## 为什么这招几乎通吃

网站的倍速按钮只是"建议"，真正的开关在浏览器手里。只要播放器基于原生 `<video>` 标签，你在 console 里就永远拥有比网站 UI 更高的权限——**UI 限制的是界面，不是能力**。

---

*起因：某天在 YouTube 想 7 倍速看视频，遂有此文。*
