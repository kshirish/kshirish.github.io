---
layout: post
title:  "Elementry js series - II"
date:   2016-06-17 21:09:00
author: kshirish
categories: js
---

### Dataset
{% highlight javascript %}
    // get data attribute 'propertyName'
    el.dataset.propertyName
    // set data attribute
    el.dataset.propertyName = 'some value';
{% endhighlight %}

### ClassList
{% highlight javascript %}
    var myDiv = document.querySelector('#logo');
    myDiv.classList.add('myCssClass');
    myDiv.classList.remove('myCssClass');
    myDiv.classList.toggle('myCssClass');
    myDiv.classList.contains('myCssClass');
{% endhighlight %}

### Handy DOM apis
{% highlight javascript %}
    // show 
    el.style.display = '';
    // hide
    el.style.display = 'none';
    // append
    el.insertAdjacentHTML('beforeend', htmlString);
    // prepend
    el.insertAdjacentHTML('afterbegin', htmlString);
    // after
    el.insertAdjacentHTML('afterend', htmlString);
    // before
    el.insertAdjacentHTML('beforebegin', htmlString);
    // append
    parent.appendChild(el);
    // clone
    el.cloneNode(true);
    // children
    el.children;
    // contains
    el.contains(child); // true or false
    // set/get attribute
    el.getAttribute('id');
    el.setAttribute('id', 'container');
    // get style 
    getComputedStyle(el)[cssRuleName];
    // parent
    el.parentNode
{% endhighlight %}

### xhr2
> `xhr2` isn't `HTML5`, it's part of the incremental improvements browser vendors are making to the core platform.

{% highlight javascript %}
    // Fetching a file as a binary blob
    var xhr = new XMLHttpRequest();
    xhr.open("GET", "http://localhost/image.jpg");
    xhr.responseType = "blob";
    xhr.onload = function response(e) {
       var urlCreator = window.URL || window.webkitURL;
       // Note: .response instead of .responseText
       var imageUrl = urlCreator.createObjectURL(this.response);
       
       document.querySelector("#image").src = imageUrl;
    };
    xhr.send();
{% endhighlight %}

{% highlight javascript %}
    var form = document.querySelector('#myform');
    var formData = new FormData(form);
    formData.append('username', 'johndoe');
    formData.append('id', 123456);

    var xhr = new XMLHttpRequest();
    xhr.open('POST', '/server', true);
    xhr.onload = function(e) {
        // do something with response 
    };

    xhr.send(formData);
{% endhighlight %}

{% highlight javascript %}
    var uploadedFiles = document.querySelector('input[type="file"]').files;
    var formData = new FormData();

    for (var i = 0; i < uploadedFiles.length; i++) {
        formData.append(uploadedFiles[i].name, uploadedFiles[i]);
    }

    var xhr = new XMLHttpRequest();
    xhr.open('POST', 'my/url/', true);
    xhr.onload = function(e) {
        // do something with response
    };

    xhr.send(formData); // multipart/form-data   
{% endhighlight %}

### Cross Origin Resource Sharing (CORS)
Normally if you tried to make an AJAX call from one domain to another, the request would fail and the browser would throw an origin mismatch error. With CORS, a domain can choose to allow requests from another domain by simply adding a header.
- Allow one website: `Access-Control-Allow-Origin: http://example.com` 
- Allow everyone: `Access-Control-Allow-Origin: *`

### References
- [Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [Image](http://i.stack.imgur.com/UfXRZ.png)
- [QuirksMode](http://www.quirksmode.org/js/events_order.html#link4)
- [YouMightNotNeedJquery](http://youmightnotneedjquery.com/)
- [David Walsh](https://davidwalsh.name/)
- [HTML5Rocks](http://www.html5rocks.com/)