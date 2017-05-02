---
layout: post
title: 跨平台的网站兼容方案
date: 2015-12-11 15:27:31
disqus: yes
---

交代一下背景，因为同事离职接手了问卷的答题前端。这个前端页面主要有以下展示场景

1. 在国内各大媒体网站跳转展示，主要是 PC 端的主流浏览器
2. 微信二维码扫描浏览，包括微信内的浏览器和移动端主流浏览器
3. 移动客户端内 SDK 展示，也就是 WebView

同事之前的技术方案是以 vanilla JS 为基础，在这个基础之上为了兼容低版本 IE 引入了 [Sizzle](https://sizzlejs.com), 为了兼容移动端引入了 [FastClick](https://ftlabs.github.io/fastclick) 和针对移动端的模板和样式，最后还有一部分和 SDK 通信的代码。最终就形成了以 vanilla JS 为基础的4个分开维护的版本。日常的开发都是以 vanilla JS 这版为核心代码，调试通过后再把这部分代码去替换其他版本内的核心代码再挨个调试。最终除了 SDK 那一版需要单独用 [iTools](http://sale.itools.cn) 打包，剩下的都是部署到服务器上通过 NGINX 判断请求的 UA 来分发对应的版本。

接手之后没多久恰好碰上设计改版和新题型的需求，就借着这个机会开始考虑重构了。主要目的就是用前端工程化的手段解决多版本共存的问题。

首先要解决的问题就是怎么能自动化的处理这4个版本的核心代码共享的问题。我的解决方案是模块化加构建工具，当时具体的技术方案是 Backbone + Grunt，模块加载器使用的是 [Dexter.Yy](https://twitter.com/dexteryy) 的 [OzJS](http://ozjs.org/)。大概的思路是分4个目录，分别是 「base」, 「ie」, 「mobile」, 「sdk」, 每个目录下的结构都是下面这种

```
├── index.js
├── models
├── tempaltes
└── views
```

这样在日常开发时，「ie」, 「mobile」, 「sdk」下的模块都是直接继承「base」中对应的模块。最终通过构建可以得到4个对应版本的文件。

核心代码共享问题有了解决方案后就是怎么兼容各个平台的浏览器了。理想方案是共用同一套代码，但是这样就会有两个问题，一个是 IE，一个是移动端。IE 下的问题想必不用多说。而移动端的问题主要是怎么让一套代码 同时支持 PC 端同时又支持移动端。

首先是解决 IE 下的样式问题，在过去通常是用 CSS Hacks 的方式来处理，一直觉得不够直观。所以采用的是通过微软提供的[版本向量](https://msdn.microsoft.com/zh-cn/library/cc817577.aspx)给 `html` 加上对应 IE 的 `class`。

{% highlight html %}
<!--[if IE 6]><html class="ie6"><![endif]-->
<!--[if IE 7]><html class="ie7"><![endif]-->
<!--[if IE 8]><html class="ie8"><![endif]-->
<!--[if IE 9]><html class="ie9"><![endif]-->
<!--[if gt IE 10]><!--> <html> <!--<![endif]-->

</html>
{% endhighlight %}

这样的话，在针对各个版本 IE 下的样式修复就成了这样。

{% highlight css %}
.selector {}
.ie6 .selector {}
.ie7 .selector {}
.ie8 .selector {}
{% endhighlight %}

然后再是处理移动端的问题。移动端也有两个问题，一个是样式问题，另一个在 `tap` 事件的问题。 

在样式上自然是用 CSS 的[媒体查询](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries) 来处理。

{% highlight css %}
.selector {}

@media (max-width: 768px) {
  .selector {}
}
{% endhighlight %}

也可以独立出一个 CSS 文件结合[版本向量](https://msdn.microsoft.com/zh-cn/library/cc817577.aspx)一起使用。

{% highlight html %}
<!--[if gte IE 9]><!-->
  <link rel="stylesheet" href="mobile.css" media="(max-width: 768px)" charset="utf-8">
<!--<![endif]-->
{% endhighlight %}

而 `tap` 事件主要是因为在引入 Backbone 后同时也引入了 Backbone 依赖的 jQuery 和 underscore，由于 jQuery 无法处理移动端的 `tap` 事件，在当时处理这个问题要么引入 FastClick，要么放弃 jQuery 使用 Zepto。在两个方案中我偏向使用 Zepto，因为 Backbone 也是支持 Zepto 的，使用方式上也和 jQuery 完全一样，最重要的是 Zepto 的 `tap` 是可以兼容 PC 端的 `click` 的。于是这个问题最后就变成怎么让 jQuery 和 Zepto 共存，后来发现通过构建，完全可以弄出两个版本的 JS 文件结合[版本向量](https://msdn.microsoft.com/zh-cn/library/cc817577.aspx)完美处理。也就是现代的浏览器和移动端的浏览器用 Zepto 版本，而低版本 IE 就用 jQuery 版。

{% highlight html %}
<!--[if lte IE 9]>
  <script src="app-jquery.js" type="text/javascript" charset="utf-8"></script>
<![endif]-->
<!--[if !IE]><!-->
<script src="app-zepto.js"></script>
{% endhighlight %}

经过这样一番重构，把原先代码重合度非常高的几个项目整合到一起通过前端工程化的手段做到最大限度的代码复用，并且替换了原先通过 NINGX 分发版本的方案，采用前端的技术解决了各个平台浏览器的兼容问题。
