---
layout: post
title:  "Configstore | Node module"
date:   2015-08-28 07:33:07
author: kshirish
categories: nodejs
---

#### First things first, do `npm install configstore`    

As the name speaks for itself, it helps you configure your config file.

#### Locate the config file
{% highlight javascript %}

    var Configstore = require('configstore');
    var config = new Configstore('my-app-config');
    // will be created at `~/.config/configstore/my-app-config.json`

{% endhighlight %}

#### Operations
{% highlight javascript %}

    // set values
    config.set('name', 'Bush');
    config.set('says', 'I am a killer.');

    config.set('address', {
     city: 'bangalore',
     state: 'KA',
     street: {
        flatno: 135,
        block: 'snowdrop'
     }
    });

    console.log(config.size);

    // delete the property value
    config.del('says');

    // get the property value
    console.log(config.get('name'));

    // get all
    console.log(config.all); 

    // reset all
    config.all = {
        msg: 'Go to sleep.'
    } 

{% endhighlight %}


Check out [more](https://www.npmjs.com/package/configstore)

