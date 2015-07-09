---
layout: post
title:  "Scary design patterns"
date:   2015-07-09 23:06:35
categories: js
---

Design patterns are scary, though they gets lighter when you see the code.
Most of us write `Modular` pattern but rarely realize it. Let's look at the basic idea behind the popular patterns and understand through code.

# Creational pattern

>Idea: creating new things

{% highlight javascript %}
var obj = {};

// Other ways
// var obj = Object.create(null);
// var obj = new Object();
// var obj = Object();

obj.property1 = 'Hello';
obj['property2'] = 'World';
Object.defineProperty(obj, 'property3', {
    value: '!',
    writable: true,         // If not mentioned, all three values are set as false
    enumerable: true,
    configurable: true
});

Object.defineProperties(obj, {
    'property4': {
        value: 'one more ',
        writable: true,         // If not mentioned, all three values are set as false
        enumerable: true,
        configurable: true
    }, 
    'property5': {
        value: 'time',
        writable: true,         // If not mentioned, all three values are set as false
        enumerable: true,
        configurable: true
    }   
});

obj.reveal = function() {

    for(var prop in this) {

        if(this.hasOwnProperty(prop)) {
            console.log(this[prop]);
        }
    }
};

obj.reveal();
{% endhighlight %}


# Constructor Pattern
>Idea: creating specific type of objects

{% highlight javascript %}
function TwitterAccount() {

    function someEncryption() {
        console.log('Just made your password more secure.');
        return 'secure password';
    }

    this.username = username;
    this.password = someEncryption(password);
    this.image = 'default Avatar';
    console.log('Your new account has been created.');
} 

TwitterAccount.addCity = function(city) {
    this.city = city;
    console.log('Your info has been saved.');
};

TwitterAccount.addImage = function(image) {
    this.image = image;
    console.log('Your info has been saved.');
};

var inLoveWithHashTags = new TwitterAccount('HashYo', 'HashLover');
inLoveWithHashTags.addCity('HashTown');
inLoveWithHashTags.addImage('HashPng');
{% endhighlight %}

# Singelton Pattern
>Idea: provides a single instance to interact with for everyone

{% highlight javascript %}
var Singelton = (function() {

    var instance;
    var Project = function() {
        this.baseUrl = '/';
        this.VERSION = '1.2';
        this.currentUrl = '/homepage';
    };

    Project.prototype.setUrl = function(pagename) { 
        this.currentUrl = baseUrl + pagename;
    };

    return {
        // returns same instance of `Project` no 
        // matter how many times called
        getInstance: function() {
            instance = instance || (new Project());
            return instance;
        }
    };
})();

var instance1 = Singelton.getInstance();
var instance2 = Singelton.getInstance();

instance1 === instance2;    //true
{% endhighlight %}

# Module Pattern
>Idea: an exposed public api which gives access to a common hidden code

{% highlight javascript %}
var AnalyticsModule = (function() {

    var viewCounter = 0;
    return {
        increaseViews: function() {
            viewCounter++;
        },
        getTotalViews: function() {
            return viewCounter;
        },
        resetViews: function() {
            viewCounter = 0;
        }
    }
})();
{% endhighlight %}

# Observer Pattern aka Publish/Subscribe
>Idea: one object watches the other loose coupling

{% highlight javascript %}
/* jQuery Tiny Pub/Sub - v0.7 - 10/27/2011
 * http://benalman.com/
 * Copyright (c) 2011 "Cowboy" Ben Alman; Licensed MIT, GPL */
 
(function($) {
 
  var o = $({});
 
  $.subscribe = function() {
    o.on.apply(o, arguments);
  };
 
  $.unsubscribe = function() {
    o.off.apply(o, arguments);
  };
 
  $.publish = function() {
    o.trigger.apply(o, arguments);
  };
 
}(jQuery));

// basic pubsub without jQuery
var pubsub = (function() {

  var pubsubModel = {};

  return {
    subscribe: function(event, handler) {

      pubsubModel[event] = pubsubModel[event] || [];
      pubsubModel[event].push(handler);

    },
    unsubscribe: function(event, handler) {

      var handlerString = handler.toString();
      pubsubModel[event] = pubsubModel[event].filter(function(h) {
        
        return h.toString() !== handlerString;
      });
    },
    publish: function(event, data) {

      pubsubModel[event].forEach(function(func) {
        func(data);
      });
    }
  };

})();
{% endhighlight %}

# Facade Pattern
>Idea: to show a different face, conceal the reality

{% highlight javascript %}
var addMyEvent = function( element, event, handler ){
  
  if(element.addEventListener){
  
    element.addEventListener( event, handler, false );
  
  }else if(element.attachEvent){
  
    element.attachEvent( 'on'+ event, handler );
  
  } else{
  
    element['on' + event] = handler;
  
  }

};
{% endhighlight %}

# Factory Pattern
>Idea: create mutiple type of objects through same interface

{% highlight javascript %}
function Elephant() {
  this.ear = 2;
  this.career = 'zoo';
  this.trunk = 'only one'
}
 
function Zebra() {
  this.skin = 'stripes';
  this.lookalike = 'donkey';
}

function Pig() {
  this.loves = 'shit';
  this.motto = 'eat & sleep';
} 

function AnimalFactory() {}

AnimalFactory.prototype.giveMe = function (option) {
  
  var parentClass = null;
  
  if( options.animType === 'elephant' ) {

    parentClass = Elephant;

  } else if( options.animType === 'zebra' ) {

    parentClass = Zebra;

  } else if( options.animType === 'pig' ) {

    parentClass = Pig;

  }
  
  if( parentClass === null ) {

    return false;

  }
  
  return new parentClass( options );

}
 
// example usage
var zoo = new AnimalFactory();

var pig = zoo.giveMe({ animType: 'pig' });

var zebra = zoo.giveMe({ animType: 'zebra' });
 
console.log( pig instanceof Pig );
console.log( zebra instanceof Zebra );
{% endhighlight %} 

# Decorator Pattern 
>Idea: when making subclass doesn't make sense

{% highlight javascript %}
// Example 1.

function Vehicle( vehicleType ){

    this.vehicleType = vehicleType || 'car';
    this.model = 'default';
    this.license = '00000-000';
}


// This is how a basic instance looks like
var plane = new Vehicle('plane');   

var truck = new vehicle('truck');

// Instead of making a seperate `truck` subclass of vehicle
// better we will decorate it.
// Let the decoration begin
truck.setModel = function( modelName ){     
    this.model = modelName;
}

truck.setColor = function( color ){
    this.color = color;
}

truck.setModel('CAT');
truck.setColor('blue');

// Here 'Vehicle' works as before
var car = new vehicle('car');

// Example 2.
// What we're going to decorate
function MacBook() {
    this.cost = function () { return 997; };
    this.screenSize = function () { return 13.3; };
}

// Decorator 1
function Memory( macbook ) {
    var v = macbook.cost();
    macbook.cost = function() {
        return v + 75;
    }
}

// Decorator 2
function Engraving( macbook ){
    var v = macbook.cost();
    macbook.cost = function(){
        return v + 200;
    };
}

// Decorator 3
function Insurance( macbook ){
    var v = macbook.cost();
    macbook.cost = function(){
        return v + 250;
    };
}

var macAir = new MacBook();

Memory(macAir);     // decorating once
Engraving(macAir);  // decorating again
Insurance(macAir);  // how many times i want
{% endhighlight %}