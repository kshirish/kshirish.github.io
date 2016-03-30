---
layout: post
title:  "Web Modules"
date:   2016-03-29 19:48:00
author: kshirish
categories: js
---

#### Problems
- Web sites are turning into Web apps
- Code complexity grows as the site gets bigger
- Developer wants discrete JS files/modules

#### Solution
- Some sort of `#include/import/require`
- Ability to load nested dependencies

### CommonJS
The dominant incarnation of this specification is `Node.js` modules (`Node.js` modules have a few features that go beyond CJS). 

Characteristics:
- Compact syntax
- Designed for synchronous loading
- Main use: server

#### Basic example
{% highlight javascript %}

    // Works sync and hurts page performance
    var Animal = require('Animal');
    var zoo = [];

    // exporting `Zoo` module
    exports.Zoo = function() {
        return {
            getAnimalsCount: function() {
                return zoo.length;
            },
            addAnimal: function(name) {
                // Will give error, if require were async
                zoo.push(new Animal(name));
            }
        };
    };
{% endhighlight %}

### AMD - Asynchronous Module Definition

A JavaScript specification that defines an API so that we can
encapsulate a piece of code into a useful unit and also export it 
to other units of code to use.

> AMD specification is implemented by Dojo Toolkit, RequireJS etc.

#### Basic exmaple
{% highlight javascript %}

    // Works async
    define('Zoo', ['Animal'], function(Animal) {
        var Animal = require('Animal');
        var zoo = [];
        
        // returning `Zoo` module
        return {
            getAnimalsCount: function() {
                return zoo.length;
            },
            addAnimal: function(name) {
                zoo.push(new Animal(name));
            }
        };
    })
{% endhighlight %}

#### Conditional loading example
{% highlight javascript %}
    define('foo', function(require) {
        if (false) {
            require(['https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js'], function () {
                console.log(jQuery);
            });
        }
    });
    require(['foo']);
{% endhighlight %}

### UMD - Universal Module Definition
Since CommonJS and AMD styles have both been equally popular. This has brought about the push for a *universal* pattern that supports both styles.

> The pattern is both AMD and CommonJS compatible, as well as supporting the old-style *global* variable definition.

{% highlight javascript %}
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['jquery', 'underscore'], factory);
    } else if (typeof exports === 'object') {
        // Node, CommonJS-like
        module.exports = factory(require('jquery'), require('underscore'));
    } else {
        // Browser globals (root is window)
        root.returnExports = factory(root.jQuery, root._);
    }
}(this, function ($, _) {
    //    methods
    function a(){};         // private
    function b(){};         // public
    function c(){};         // public

    return {
        b: b,
        c: c
    }
}));
{% endhighlight %}

#### References
- [Require JS](http://requirejs.org/docs/whyamd.html)
- [Require JS](http://requirejs.org/docs/why.html)
- [Addy Osmani](https://addyosmani.com/writing-modular-js/)




