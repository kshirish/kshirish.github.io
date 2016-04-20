---
layout: post
title:  "Essential functions"
date:   2016-03-27 17:26:00
author: kshirish
categories: js
---


### debounce
> Returns a function which will be executed only if N milliseconds have passed without it being called. If `immediate` is passed, trigger the function on the leading edge, instead of the trailing.
> 

#### implementation
{% highlight javascript %}
  function debounce(func, wait, immediate) {
    
    var timeout;
    
    return function() {
      
      var context = this;
      var args = arguments;
      var callNow = immediate && !timeout;

      clearTimeout(timeout);
      timeout = setTimeout(function() {
        timeout = null;
        !timeout && func.apply(context, args);          
      }, wait);

      callNow && func.apply(context, args);          
    };
  }
{% endhighlight %}

#### usage
{% highlight javascript %}
  var func = debounce(function() {
    console.log('Debounced!');
  }, 500);

  window.addEventListener('resize', func);
{% endhighlight %}

### throttle
> Returns a function which will be executed at most once every N milliseconds.

#### implementation
{% highlight javascript %}
  function throttle(func, wait) {
    
    var isTime = true;
    
    return function() {
      var args = arguments;
      if(isTime) {
        isTime = false;
        func.apply(this, arguments);
        setTimeout(function() {
          isTime = true;
        }, wait);
      }
    };
  }
{% endhighlight %}

#### usage
{% highlight javascript %}
  var func = throttle(function() {
    console.log('Throttled!');
  }, 500);

  window.addEventListener('resize', func);
{% endhighlight %}

### once
> Executes a function only once.

#### implementation
{% highlight javascript %}
  function once(func) {
    
    var result;
    
    return function() {
      if(func) {
        result = func.apply(this, arguments);
        func = null;
      }

      return result;
    }
  }
{% endhighlight %}

#### usage
{% highlight javascript %}
  var displayOnce = once(function() {
    console.log('Hello!');
  });

  displayOnce();  // Hello!
  displayOnce();  // undefined
  displayOnce();  // undefined
{% endhighlight %}

### curry
> A way of constructing functions that allows partial application of a function’s arguments. Therefore, either you can pass all of the arguments a function is expecting and get the result, or pass a subset of those arguments and get a function back that’s waiting for the rest of the arguments.

#### implementation
{% highlight javascript %}
  function curry(fx) {
    // no of arguments that `fx` is expecting
    var arity = fx.length;

    return function f1() {
      var args = [].slice.call(arguments, 0);
      if (args.length >= arity) {
        return fx.apply(null, args);
      }
      else {
        return function f2() {
          var args2 = [].slice.call(arguments, 0);
          // recursion logic:-
          // call `f1` with accumulated arguments
          return f1.apply(null, args.concat(args2)); 
        }
      }
    };
  }
{% endhighlight %}

#### usage
{% highlight javascript %}
  var sumFour = curry(function(w, x, y, z) {
    return w + x + y + z;
  });
 
  var f1 = sumFour(10);         // returns a function awaiting 3 arguments
  var f2 = sumFour(1)(2, 3),    // returns a function awaiting 1 argument
  var f3 = sumFour(1, 2, 7),    // returns a function awaiting 1 argument
  var x1 = sumFour(1, 2, 3, 4); // fully applied
  var x2 = sumFour(1)(2)(3)(4); // fully applied

{% endhighlight %}

### compose
> Returns a complex function, which will be composed out of given functions i.e. a, b and c. 
> Expression - a(b(c(x))) - with x being the starting value. 
> 

#### implementation
{% highlight javascript %}
  var compose = function () {

    var fns = [].slice.apply(arguments);
    var val;  

    return function() {

      var tmp = [].slice.apply(arguments);

      for(var i = 0; i < fns.length; i++) {
        tmp = [fns[i].apply(this, tmp)];
      }

      return tmp[0];
    };
  };
{% endhighlight %}

#### usage
{% highlight javascript %}
  var greet = function(name) {
    return 'Hi, ' + name;
  };

  var getName = function(first, middle, last) {
    return first + ' ' + middle + ' ' + last;
  };

  var sayHi = compose(getName, greet, function(r) {console.log(r);});
  sayHi('ram', 'gopal', 'verma');   // Hi, ram gopal verma
{% endhighlight %}

### References
- [David Walsh](https://davidwalsh.name/essential-javascript-functions)





