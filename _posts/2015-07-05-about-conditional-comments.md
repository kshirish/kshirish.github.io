---
layout: post
title:  "Conditional comments!"
date:   2015-07-05 18:06:35
author: kshirish
categories: html
---

To keep it short - IE doesn't walk along well with other modern web browsers. Many features of `HTML5` and `CSS3` are either partially implemented or not provided at all. See the [comparison](https://html5test.com/compare/browser/ie-9.html) for yourself. 

Let me list out some of the stuff `IE9` doesn't support:

- Application Cache (offline)
- Web Workers (threads in JavaScript)
- HTML5 Forms (validation mechanism, CSS3 selectors)
- JavaScript Strict Mode
- WebGL (3D)
- CSS3 Transitions (for animations)
- CSS3 Text Shadow
- CSS3 Gradients
- CSS3 Border Image
- CSS3 Flex box model
- ClassList APIs
- FormData
- HTML5 History API
- Drag'n Drop from Desktop

Since it has substantial amount of users, it becomes inevitable to ignore it. Also tools like `Modernizr` makes it easy for you to write conditional JavaScript and CSS to handle each situation, whether a browser supports a feature or not.

These conditional comments are only supported till `IE9` (from `IE5`). So if you need special CSS styles for `IE10` or up you’ll have to find another method, since conditional comments are disabled in `IE10`.

{% highlight html %}
<!--[if IE]>
According to the conditional comment this is IE5-IE9<br />
<![endif]-->
<!--[if IE 6]>
According to the conditional comment this is IE6<br />
<![endif]-->
<!--[if IE 7]>
According to the conditional comment this is IE7<br />
<![endif]-->
<!--[if IE 8]>
According to the conditional comment this is IE8<br />
<![endif]-->
<!--[if IE 9]>
According to the conditional comment this is IE9<br />
<![endif]-->
<!--[if gte IE 8]>
According to the conditional comment this is IE8 or higher<br />
<![endif]-->
<!--[if lt IE 9]>
According to the conditional comment this is IE lower than 9<br />
<![endif]-->
<!--[if !IE]>
According to the conditional comment this is not IE5-IE9<br />
<![endif]-->
{% endhighlight %}

### Conditional linking to stylesheets

{% highlight html %}
<link rel="stylesheet" href="normal-styles.css" type="text/css" />
<!--[if lte IE 9]>
<link rel="stylesheet"  href="ie-fixes.css" type="text/css" />
<![endif]-->
{% endhighlight %}

Check here [IE10 Detection](http://stackoverflow.com/questions/16135814/check-for-ie-10)
