---
layout: post
title:  "How digest cycle works"
date:   2016-03-22 10:16:07
author: kshirish
categories: angular
---

I was awestruck by the feature of two-way data-binding which was first introduced by Angular. It was a big deal and then quickly became core to any upcoming framework. Let's unveil the process behind it step by step.

### Event Loop  

Javascript never blocks (avoid legacy exceptions i.e. `alert` and synchronous XHR). Handling I/O is typically performed via events and callbacks. A browser's
event model consists of three things -

- call stack 
- webapis (Event listeners, ajax, setTimeout)
- async queue (callbacks)

{% highlight javascript %}

    console.log('Hello');
    setTimeout(function() {
        console.log('!');
    }, 300);
    console.log('World');

{% endhighlight %}

Above code outputs `HelloWorld!`. The job of Event loop model is simple

> Wait for call stack to get empty and then push from async queue (if something is there ) to call stack.

### $watch list     

{% highlight html %}
    <ul>
      <li ng-repeat="person in people">
          <span ng-bind="person.name"></span> -
          <span ng-bind="person.age"></span>
      </li>
    </ul>
{% endhighlight %}


{% highlight javascript %}

    app.controller('MainCtrl', function($scope) {
      $scope.people = [{
        name: 'Jim',
        age: 11
      }, {
        name: 'Kim',
        age: 12
      }, {
        name: 'Tim',
        age: 13
      }];
    });

{% endhighlight %}

Above we have created (3*2) + 1 = 7 watches in total. Two per person and one for `ng-repeat` which is watching `people`. This entire list of watchers is called `$watch` list. Properties that are on the `$scope` object are only bound if they are used in the view.

- `Dirty checking` is checking whether a value has changed or not. Angular walks down the `$watch` list one by one, and, if the updated value has not changed from the old value, it continues down the list. If the value has changed, the app records the new value and continues down the `$watch` list.

- Once Angular has run through the entire $watch list, if any value changed, the app will re-run the $watch loop until it finds that nothing has changed.

### $evalAsync

To defer an execution to some point in the future, we can use `$timeout` or `$evalAsync`. The preferred way to do it is by using the `$timeout` service that integrates the delayed function to the `$digest` lifecycle with `$apply`.

But there is also another way to defer code in Angular, and that's the `$evalAsync` function on `$scope`. It takes a function and schedules it to run later but still during the ongoing digest or before the next digest or before DOM rendering.




