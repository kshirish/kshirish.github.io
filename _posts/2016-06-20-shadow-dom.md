---
layout: post
title:  "Shadow DOM"
date:   2016-06-20 10:00:00
author: kshirish
categories: [js, html5]
---

### Why do I care?
Before even going into what, when and how, let's ask ourselves why do we even bother to learn about this strange thing that call itself - *Shadow DOM*. 

Great start! Web Component standards encourage to use more of a reusable widget/component based approach. But there is a fundamental problem that makes widgets built out of HTML and JavaScript hard to use - 

> The DOM tree inside a widget isn’t encapsulated from the rest of the page. This lack of encapsulation means your document stylesheet might accidentally apply to parts inside the widget; your JavaScript might accidentally modify parts inside the widget; your IDs might overlap with IDs inside the widget; and so on.

Thus, Shadow DOM addresses the DOM tree encapsulation problem.

### What is Shadow DOM?
With Shadow DOM, elements can get a new kind of node associated with them. This new kind of node is called a **shadow root**. An element that has a shadow root associated with it is called a **shadow host**. The content of a shadow host isn’t rendered; the content of the shadow root is rendered instead.

Check the [Support](http://caniuse.com/#search=shadow%20dom)

### See it in action
{% highlight html %}
    <html>
      <body>
        <h1 id="foo">Hi, I am your host.</h1>
        <script>
          // create shadow DOM on the <h1> element above
          var foo = document.getElementById('foo');
          var shadow = foo.createShadowRoot();
          shadow.innerHTML = '<p>Hi, I am your shadow.</p>';
          console.log(foo.textContent); // Hi, I am your host.
        </script>
      </body>
    </html>
{% endhighlight %}

### First step to style Shadow DOM
* By default usual styling won't get applied here i.e. it only works for main DOM tree but not for shadow trees. For it, we can do something like
  `shadow.innerHTML = '<style>p { color: red; }</style>';`
* `:host` allows you to select and style the element hosting a shadow tree.
  `shadow.innerHTML = '<style>:host { text-transform: uppercase; }</style>';`

### Better roads with `<template>`
{% highlight html %}
    <html>
      <body>
        <h1 id="foo">Hi, I am your host.</h1>

        <template id="shadowTemplate">

          <style>
            h2 {color: red;}
            h3 {color: green;}
            h4 {color: yellow;}
            h5 {color: orange;}    
          </style>

          <h2>I am your shadow.</h2>
          <h3>me too.</h3>
          <h4>me tooo.</h4>
          <h5>me toooo.</h5>  

        </template>
        <script>
          var foo = document.getElementById('foo');
          var shadow = foo.createShadowRoot();
          var template = document.getElementById('shadowTemplate');
          var clone = document.importNode(template.content, true);
          shadow.appendChild(clone);
        </script>
      </body>
    </html>
{% endhighlight %}

### Content and presentation

{% highlight html %}
    <html>
      <body>
        <h1 id="foo">Hi, I am your host.</h1>

        <template id="shadowTemplate">

          <style>
            ...
          </style>

          <!--  Content of shadow host will be injected here. -->
          <content></content>
          <h2>I am your shadow.</h2>
          <h3>me too.</h3>
          <h4>me tooo.</h4>
          <h5>me toooo.</h5>  

        </template>
        <script>
          ... 
        </script>
      </body>
    </html>
{% endhighlight %}

As soon as the shadow host's content changes, shadow DOM will be updated automatically. By separating content and presentation, we can simplify the code that manipulates the content.

>  The content is in the document; the presentation is in the Shadow DOM. They are automatically kept in sync by the browser when it comes time to render something. Also, now since we have isolated the content from presentation, we can change our presentation and the content doesn't need to know about it. 

### Advance `<content>`
{% highlight html %}
    <h1 id="foo">
        <p>bald and fat.</p>
        <i>30 years old.</i>
        <div class="warning">I am the future.</div>    
    </h1>
    <template id="shadowTemplate">

    <style>
        h2 {color: red;}
        h3 {color: green;}
        h4 {color: yellow;}
        h5 {color: orange;}    
    </style>

      <div style="color: red;">
        <content select=".warning"></content>
      </div>

      <div style="color: blue;">
        <content select="i"></content>
      </div>

      <div style="color: yellow;">
        <content select="p"></content>
      </div>


    <h3>me too.</h3>
    <h4>me tooo.</h4>
    <h5>me toooo.</h5>    
    </template>
{% endhighlight %}

* `select` can only select elements which are immediate children of the host node. That is, you cannot select descendants (e.g. `select="table tr"`).
* Content once `select`ed, cannot be `select`ed again.

### Styling host
{% highlight html %}
  <style>
    :host(.different) { 
      /* Applies if the host has a class 'different'. */
    }

    :host(.different:host) { 
      /* Same as above. */
    }

    :host(div) {  {
      /* Applies if the host element is a <div>. */
    }
  </style>
{% endhighlight %}

### Piercing through the shadow DOM's boundary
{% highlight html %}
  <style>
    #foo::shadow span {
      color: red;
    }
  </style>

  <div id="foo">
    <span>Light DOM</span>
  </div>

  <script>
    var foo = document.querySelector('#foo');
    var root = foo.createShadowRoot();
    root.innerHTML = '<span>Shadow DOM</span><content></content>';
  </script>
{% endhighlight %}

### The ruthless /deep/
> Completely ignores all shadow boundaries and crosses into any number of shadow trees. 

The `/deep/` combinator is particularly useful in the world of Custom Elements where it's common to have multiple levels of Shadow DOM. Prime examples are nesting a bunch of custom elements (each hosting their own shadow tree) or creating an element that inherits from another using `<shadow>`.

{% highlight html %}
  <style>
    /* select all <x-panel> elements that are descendants of <x-tabs>, anywhere in the tree */
    x-tabs /deep/ x-panel {
      ...
    }

    /* style all elements that have the class .library-theme, ignoring all shadow trees */
    body /deep/ .library-theme {
      ...
    }
  </style>
{% endhighlight %}

### Shadow DOM traversal
{% highlight javascript %}
  document.querySelector('x-tabs::shadow x-panel::shadow #foo');
{% endhighlight %}

### Does all of this apply on native elements too?
> Native HTML controls are a challenge to style. With `::shadow` and `/deep/`, any element in the web platform that uses Shadow DOM can be styled.

{% highlight html %}
  <style>
    video /deep/ input[type="range"] {
      background: hotpink;
    }
  </style>
{% endhighlight %}

### ::content pseudo element
{% highlight html %}
  <div id="foo">
    <section>
      <div>I'm not underlined</div>
      <p>I'm underlined in Shadow DOM!</p>
    </section>
  </div>

  <script>
    var foo = document.querySelector('#foo');
    var root = foo.createShadowRoot();
    root.innerHTML = `
      <style>
        ::content section p {
          text-decoration: underline;
        }
      </style>
      <h3>Shadow DOM</h3>
      <content select="section"></content>`;
  </script>
{% endhighlight %}

### User states (:hover, :focus, :active, etc.)
{% highlight html %}
  <style>
    :host {
      ...
    }
    :host(:hover) {
      ...
    }
    :host(:active) {
      ...
    }
  </style>
{% endhighlight %}

### Theming
> The `:host-context(<selector>)` pseudo class matches the host element if it or any of its ancestors matches `<selector>`

{% highlight html %}
  <body class="different">
    <x-foo></x-foo>
  </body>

  :host-context(.different) {
    color: red;
  }
{% endhighlight %}

### Multiple Shadow DOMs
* Obviously later one will replace previous one.
  {% highlight html %}
    <div id="foo">I am your host.</div>
    <script>
        var foo = document.getElementById('foo');
        var root1 = foo.createShadowRoot();
        var root2 = foo.createShadowRoot();
        root1.innerHTML = '<div>Root 1</div>';
        root2.innerHTML = '<div>Root 2</div>';
    </script>
{% endhighlight %}

* **Shadow insertion points** (`<shadow>`) are similar to normal insertion points (`<content>`) in that they're placeholders. However, instead of being placeholders for a host's content, they're hosts for other shadow trees.
{% highlight html %}
    <div id="foo">I am your host.</div>
    <script>
        var foo = document.getElementById('foo');
        var root1 = container.createShadowRoot();
        var root2 = container.createShadowRoot();
        root1.innerHTML = '<div>Root 1</div><content></content>';
        root2.innerHTML = '<div>Root 2</div><shadow></shadow>';
    </script>
{% endhighlight %}

* Reference to previous tree `root2.olderShadowRoot === root1;   //true`
* Obtaining a shadow root
{% highlight html %}
    var root = host.createShadowRoot();
    host.shadowRoot === root; // true
{% endhighlight %}

### References
- [Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [HTML5Rocks](http://www.html5rocks.com/)