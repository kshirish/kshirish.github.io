---
layout: post
title:  "Web workers"
date:   2016-03-23 7:06:00
author: kshirish
categories: js
---

Web workers are javascript running independently of other scripts. They don't affect the page's performance. However, they can pass messages to the other scripts and vice-versa.

### A simple example (Dedicated Worker)

{% highlight javascript %}

  // content of `worker_script.js`
  setTimeout(function() {postMessage('Yay!');}, 6000);

{% endhighlight %}

{% highlight javascript %}
  
  var w;

  if(window.Worker) {
    w = new Worker('worker_script.js');

    // receives data from `worker_script.js`
    w.addEventListener('message', function(event) {
        console.log(event.data);      // Yay!
    }, false);

    // in case your worker throws an error
    w.addEventListener('error', function(e) {
      console.log(e);
    });
  }

{% endhighlight %}

- A worker's context is represented by either `DedicatedWorkerGlobalScope` or `SharedWorkerGlobalScope` object (not window).
- `Dedicated workers`: only accessible from the script that first spawned it
- `Shared workers`: can be accessed from multiple scripts.

### Shared Worker

{% highlight javascript %}

  // content of `shared_worker.js`
  self.addEventListener('connect', function(e) {
   // an explicit port is opened in case of `Shared Workers`
   // get the port, since there might be a lot of ports 
    var port = e.ports[0];
    port.addEventListener('message', function(event) {
      console.log(event.data)
      port.postMessage('Hello Back');
    });
  });

{% endhighlight %}

{% highlight javascript %}

  var w = new SharedWorker("shared_worker.js");

  w.port.addEventListener('message', function(event) {
    console.log(event.data);
  });
  w.port.postMessage('How are you?');

{% endhighlight %}

### Features available to Workers
Due to their multi-threaded behavior, `web workers` only have access to a subset of JavaScript's features:

- `navigator` object
- `location` object (read-only)
- XMLHttpRequest
- `setTimeout()`/`clearTimeout()` and `setInterval()`/`clearInterval()`
- `Application Cache`
- Importing external scripts using the `importScripts()` method
- Spawning other web workers

Workers do NOT have access to:

- DOM (it's not thread-safe)
- `window` object
- `document` object
- parent object

### Loading External Scripts
You can load external script files or libraries into a worker. The method takes zero or more strings representing the filenames for the resources to import.

{% highlight javascript %}
  importScripts('script1.js', 'script2.js');
{% endhighlight %}

### Inline Worker

{% highlight html %}
  <!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
</head>
<body>

  <script id="worker1" type="javascript/worker">
    // This script won't be parsed by JS engines
    // because its type is javascript/worker.
    self.onmessage = function(e) {
      self.postMessage('msg from worker');
    };
    // Rest of your worker code goes here.
  </script>

  <script>    
    var blob = new Blob([document.querySelector('#worker1').textContent]);
    // Obtain a blob URL reference to our worker 'file'.
    // Blob URLs are unique and last for the lifetime of your 
    // application (e.g. until the document is unloaded).
    var blobURL = window.URL.createObjectURL(blob);
    var worker = new Worker(blobURL);
    
    // Now you got your usual `worker` object
    worker.addEventListener('message', function(e) {
      console.log("Received: " + e.data);
    });
    worker.postMessage('Hello'); // Start the worker.
  </script>
</body>
</html>
{% endhighlight %}

### So where do I use it?
- Prefetching and/or caching data for later use
- In web editors for syntax highlighting, text formatting and spell check
- Analyzing video or audio data
- Polling of webservices
- Processing large arrays/JSON responses

### References
- [HTML5Rocks](http://www.html5rocks.com/en/tutorials/workers/basics/)





