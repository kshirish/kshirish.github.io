---
layout: post
title:  "Elementry js series - I"
date:   2016-05-17 19:08:00
author: kshirish
categories: js
---

![The Chain](http://i.stack.imgur.com/UfXRZ.png)

### Data types
* Boolean
* Null
* Undefined
* Number
* String
* Symbol (new in ECMAScript 6)
* Object

### Function declaration
{% highlight javascript %}
    function square(number) {
        return number * number;
    }
{% endhighlight %}

### Function expression
{% highlight javascript %}
    var square = function (number) {
        return number * number;
    };
{% endhighlight %}

### Iterator
> An iterator-object knows how to access items from a collection one at a time. It provides a `next()` method which returns the next item in the sequence. This method returns an object with two properties: *done* and *value*.

{% highlight javascript %}
    function arrayIterator(array) {
        var nextIndex = 0;
        
        return {
           next: function() {
               return nextIndex < array.length ? {value: array[nextIndex++], done: false} : {done: true};
           }
        }
    }

    var it = makeIterator(['yo', 'ya']);
    console.log(it.next().value); // 'yo'
    console.log(it.next().value); // 'ya'
    console.log(it.next().done);  // true
{% endhighlight %}

### Iterable
> An iterable-object must have a property with a `Symbol.iterator` key. Some built-in types i.e. `Array` or `Map` have a default iteration behavior.

{% highlight javascript %}
    var myIterable = {}
    myIterable[Symbol.iterator] = function* () {
        yield 1;
        yield 2;
        yield 3;
    };

    for(value of myIterable) {
        console.log(value);
    }
{% endhighlight %}

### for...in statement
>Iterates over all the enumerable properties of an object. 

{% highlight javascript %}
    var info = {username: 'Lousy', password: 'NoSmoking!'};

    for(var key in info) {
        console.log(key);
        console.log(info[key]);
    }
{% endhighlight %}

### for...of statement
>Iterates over iterable objects i.e. `Array`, `Map`, `Set`, `arguments`, `NodeList` etc.

{% highlight javascript %}
    var images = document.querySelectorAll('img');

    for(var image of images) {
        console.log(image);
    }
{% endhighlight %}

### Object.create
{% highlight javascript %}
    // polyfill
    if (typeof Object.create != 'function') {
      Object.create = (function(undefined) {
        var Temp = function() {};
        return function (prototype, propertiesObject) {
          if(prototype !== Object(prototype) && prototype !== null) {
            throw TypeError('Argument must be an object, or null');
          }
          Temp.prototype = prototype || {};
          if (propertiesObject !== undefined) {
            Object.defineProperties(Temp.prototype, propertiesObject);
          } 
          var result = new Temp(); 
          if(prototype === null) {
            result.__proto__ = null;
          }
          Temp.prototype = null; 
          return result;
        };
      })();
    }
{% endhighlight %}

### Closure
Normally, the local variables within a function only exist for the duration of that function's execution. A closure is a special kind of object that combines two things: a function, and the environment in which that function was created.

Below, `add5` and `add10` are both closures. They share the same function body definition, but store different environments.
{% highlight javascript %}
    function makeAdder(x) {
      return function(y) {
        return x + y;
      };
    }

    var add5 = makeAdder(5);
    var add10 = makeAdder(10);

    console.log(add5(2));  // 7
    console.log(add10(2)); // 12
{% endhighlight %}

### Object.defineProperty`
{% highlight javascript %}
    var obj = {};
    var bValue = 'Billy';
    Object.defineProperty(obj, 'name', {
        __proto__: null, // no inherited properties
        get: function() { return bValue; },
        set: function(newValue) { bValue = newValue; },
        // not enumerable
        // not configurable
        // not writable
        // as defaults
    });
{% endhighlight %}

### Extend
{% highlight javascript %}
    var extend = function(isDeep, out) {
      out = out || {};

      for (var i = 1; i < arguments.length; i++) {
        var obj = arguments[i];

        if (!obj)
          continue;

        for (var key in obj) {
          if (obj.hasOwnProperty(key)) {
            if (typeof obj[key] === 'object' && isDeep)
              out[key] = extend(true, out[key], obj[key]);
            else
              out[key] = obj[key];
          }
        }
      }

      return out;
    };

    extend(true, {}, objA, objB);
{% endhighlight %}

### Event order
Any event taking place (in `W3C` event model) is first captured until it reaches the target element and then bubbles up again. So we can opt to register an event handler in the capturing or in the bubbling phase by passing `true` or `false` in the last argument of `addEventListener()` method respectively.

{% highlight html %}
    <div id="parent">
        <div id="child"></div>
    </div>
{% endhighlight %}

### Event capturing
When we use event capturing, the event handler of `parent` fires first and then the event handler of `child`.

### Event bubbling
When we use event capturing, the event handler of `child` fires first and then the event handler of `parent`.

### target/srcElement
An event has a `target` or `srcElement` property that contains a reference to the element the event happened on.

### currentTarget
It contains a reference to the element the event is currently being handled by.

### Event.preventDefault
Prevents the default action of an event without stopping further propagation of it.

### Event.stopPropagation
Prevents further propagation of the current event in the capturing and bubbling phases.

### Event.stopImmediatePropagation
If several listeners are attached to the same element for the same event type, they are called in order in which they have been added. If during one such call, `event.stopImmediatePropagation()` is called, no remaining listeners will be called.

### Event delegation
{% highlight html %}
    <ul id="parent-list">
        <li id="post-1">Item 1</li>
        <li id="post-2">Item 2</li>
        <li id="post-3">Item 3</li>
        <li id="post-4">Item 4</li>
        <li id="post-5">Item 5</li>
        <li id="post-6">Item 6</li>
    </ul>
{% endhighlight %}

{% highlight javascript %}
    document.getElementById("parent-list").addEventListener("click", function(e) {
        // e.target is the clicked element!
        // If it was a list item
        if(e.target && e.target.nodeName == "LI") {        
            // do something with the li element 
        }
    });
{% endhighlight %}

### Miscellaneous
{% highlight javascript %}
    var foo = new Function('console.log("Look! that\'s you do it!")');
    Object.getPrototypeOf(foo) === Function.prototype;  // true
{% endhighlight %}


### Wierd parts
{% highlight javascript %}
    var a;
    a + 3;  // NaN

    var b = null;
    b + 3;  // 3
    b * 3;  // 0   
{% endhighlight %}

### References
- [Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [Image](http://i.stack.imgur.com/UfXRZ.png)
- [QuirksMode](http://www.quirksmode.org/js/events_order.html#link4)
- [YouMightNotNeedJquery](http://youmightnotneedjquery.com/)
- [David Walsh](https://davidwalsh.name/)
- [HTML5Rocks](http://www.html5rocks.com/)