---
title: css变量：currentColor
date: 2016-05-12
tags: 
- css
- css3
- 前端
categories:
- 前端
---

如果你用LESS或SASS等预处理器写css，那么对样式中的变量肯定很熟悉。 今天看到一个**CSS原生变量** ——currentColor。

currentColor的值是由当前元素使用的color属性的计算值决定的。也就是说，currentColor的值和当前color属性的值是一样的，这也是currentColor这个名字的由来。

比如：想把所有的水平分割线(hr)自动与文本的颜色保持一致，可以这样写

{% codeblock lang:css %}
	hr {
	  height:.5em;
	  background:currentColor;
	}
{% endcodeblock %}

<!-- more -->

如果没有设置默认值，浏览器会默认黑色。  如果设置了父元素的color值，currentColor的值则继承其父元素的值。 如下所示：
<p data-height="265" data-theme-id="dark" data-slug-hash="ZWPgbm" data-default-tab="css,result" data-user="yangyong" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/yangyong/pen/ZWPgbm/">currentColor demo</a> by yangyong (<a href="http://codepen.io/yangyong">@yangyong</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

currentColor是很多CSS颜色属性的初始值，如 border-color、outline-color、text-shadow、box-shadow、列表项的小黑点和边框、img的alt文本 等等。


### currentColor 实例

这里有一个由Scott Kellum创建的示例，他进一步理解了这个概念，并为color属性添加了一个动画效果。在color属性值改变时，所有受到该color影响的元素都会随之改变他们的颜色。查看一下Scott Kellum (@scottkellum)在CodePen上创建的示例<a href="http://codepen.io/scottkellum/pen/Fhxql/">currentColor</a>。

这是一个使用currentColor的非常棒的示例，特别是动画效果的部分。

### 应用场景：制作UI组件
{% img /images/currentColor-components-small.gif [demo [alt text]] %}

