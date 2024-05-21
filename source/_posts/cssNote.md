---
title: cssNote
date: 2024-05-21 23:07:47
tags:
---

### 全局样式

```css
body {
  margin: 0;
  padding: 0;
  box-siziing: border-box;
}
```

### opacity和rgba
opacity和rgba都是用来设置颜色的，但是opacity和rgba的区别在于opacity是设置整个元素的透明度，而rgba是设置颜色的透明度。


### 百分比的设置
百分比是一个相对单位，需要一个参考系
* 常规元素的参考系是父元素的content-box
* 绝对/固定定位的元素参考系是父元素的padding-box

> width,padding, margin 是相对参考系的宽度

> height 是相对参考系的height，注意当父元素是由子元素的height撑开时，子元素的百分比无效，子元素的高度不固定，父也不固定所以无效

### 最大，最小宽高
一般在pc端会设置min-width
图片会设置 max-width:100
