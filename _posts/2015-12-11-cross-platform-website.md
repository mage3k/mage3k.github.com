---
layout: post
title: 跨终端的网站兼容方案
date: 2015-12-11 15:27:31
disqus: yes
---

这几天给公司的同事讲一些前端基础概念的时候讲到了一些前端兼容性的问题，让我想起了去年的一个 SPA 项目的重构。

当时的项目为了兼容低版本的 IE 浏览器和移动端的的浏览器是通过 Nginx 判断 UA 来访问不同版本的资源。而当时有 PC，IE，Mobile 三个版本， 对应三个代码库分开维护。

项目接手过来没多久就决定重构，重构的目标就是：

> Write once, run anywhere.

首先是 CSS，低版本的 IE 通过微软的[条件注释](https://msdn.microsoft.com/zh-cn/library/cc817577.aspx)来给页面的顶层节点加一个标识 IE 版本的 `class` 来做样式覆盖。

{% highlight css %}
.selector {}
.ie6 .selector {}
.ie7 .selector {}
.ie8 .selector {}
{% endhighlight %}

移动端通过 CSS 的[媒体查询](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries)来实现在指定屏幕宽度范围下做样式覆盖。

{% highlight css %}
.selector {}

@media (max-width: 768px) {
  .selector {}
}
{% endhighlight %}

如果移动端下样式覆盖比较多，可以独立出一个 CSS 文件结合上面说到的[条件注释](https://msdn.microsoft.com/zh-cn/library/cc817577.aspx)一起使用。

{% highlight html %}
<!--[if gte IE 9]><!-->
  <link rel="stylesheet" href="mobile.css" media="(max-width: 768px)" charset="utf-8">
<!--<![endif]-->
{% endhighlight %}

这样不支持[媒体查询](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries)的 IE 浏览器便不会加载 mobile.css 文件了。同理，如果 IE 下样式覆盖较多也可以独立出 CSS 文件。

然后是 JS，JS 当时的情况有点复杂，这里就不细说了，以后有时间再开另外一篇讲一下。总之最终的方案定为 Backbone ＋ gulp。大概思路是通过 gulp 构建出两个版本，一个 JQuery 版，一个 Zepto 版。通过[条件注释](https://msdn.microsoft.com/zh-cn/library/cc817577.aspx)来过滤。

{% highlight html %}
<!--[if lte IE 9]>
  <script src="app-jquery.js" type="text/javascript" charset="utf-8"></script>
<![endif]-->
<!--[if !IE]><!-->
<script src="app-zepto.js"></script>
{% endhighlight %}

之所以这么做，一个原因是因为 Zepto 体积小适合移动端，另一个原因是移动端点击事件有 300ms 的延迟需要 tap 事件的支持。

重构完成之后基本上是达到了当时的目标，具体兼容范围没有具体统计过，不过根据公司的测试机使用情况来看，PC 端的各个浏览器都表现正常，移动端上兼容 Android 2.2 以上， iOS 6.0 以上，完全通过前端的技术实现了适配。
