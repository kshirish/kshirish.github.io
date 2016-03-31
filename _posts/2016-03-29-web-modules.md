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

#### Issues
For the project of medium complexity, HTTP overheads for asynchronous downloading of single Javascript files are dramatic. Therefore, `RequireJS`  advises developers to proceed with a bundling operation of the various modules using their command line tool.

#### Bundling process
The process parses the files begining at a specified entry-point to reconstruct the dependencies on the basis of the `define()` calls that it finds along the way. It is then able to organize and concatenate all of the necessary files in a single file i.e. `bundle.js` which we will then be included in our HTML.

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

### Browserify
Unlike `RequireJS`, `Browserify` allows you to write your client-side application making use of `CommonJS`’s synchronous loading (using `Node.js`). How is this possible?

- Bundling similar to require.js `browserify app.js --outfile bundle.js`

{% highlight javascript %}
    
// let's take a look at the generated `bundle.js`
function debundle(data) {
  var cache = {};
  var require = function(name) {
    if (cache[name]) { return cache[name]; }
    var module = cache[name] = { exports: {} };
    data.modules[name](require, module);
    return module.exports;
  };
  return require(data.entryPoint);
}

debundle({
  entryPoint: "./app",
  modules: {
    "./app": function(require, module) {
      var calculator = require('./calculator');
      console.log(calculator.sum(1, 2));
    },
    "./calculator": function(require, module) {
      module.exports = {
        sum: function(a, b) { return a + b; }
      };
    }
  }
});
{% endhighlight %}

The content of every module, including the entry-point, can be requested during runtime by being passed to `debundle()` which implements the `module.exports/require` - `CommonJS` mechanism on the client side.


#### References
- [Require JS](http://requirejs.org/docs/whyamd.html)
- [Require JS](http://requirejs.org/docs/why.html)
- [Addy Osmani](https://addyosmani.com/writing-modular-js/)
- [Lean Panda](http://www.leanpanda.com/blog/2015/06/28/amd-requirejs-commonjs-browserify/)




