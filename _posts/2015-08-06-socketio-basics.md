---
layout: post
title:  "Socketio basics!"
date:   2015-08-06 20:06:35
categories: js
---

## On Client Side
{% highlight javascript %}
    // create a socket 
    // if url is not provided, then it connects to the default existing server  
    var socket = io(url);

    // listen to custom events
    socket.on('privateMessage', function(data) {
        /** when `privateMessage` event is fired **/
    });

    socket.on('universalMessage', function(data) {
        /** when `universalMessage` event is fired **/
    });
{% endhighlight %}
 
{% highlight javascript %}
    // existing events for socket object
    socket.on('connect', function() { /** when socket connects **/ });
    socket.on('error', function() { /** error occured **/ });
    socket.on('disconnect', function() { /** when socket disconnects **/ });
    socket.on('reconnect', function() { /** when socket reconnects **/ });
{% endhighlight %}

## On Server Side
{% highlight javascript %}
    io.on('connection', function(socket) { /** a new socket connects **/ });
{% endhighlight %}

### List all the sockets
{% highlight javascript %}
    io.sockets.sockets
{% endhighlight %}

### A few more tips
{% highlight javascript %}
// sending to sender-client only
 socket.emit('message', "this is a test");

 // sending to all clients, include sender
 io.sockets.emit('message', "this is a test");

 // sending to all clients except sender
 socket.broadcast.emit('message', "this is a test");

 // sending to all clients in 'game' room(channel) except sender
 socket.broadcast.to('game').emit('message', 'nice game');

 // sending to all clients in 'game' room(channel), include sender
 io.sockets.in('game').emit('message', 'cool game');

 // sending to sender client, only if they are in 'game' room(channel)
 socket.to('game').emit('message', 'enjoy the game');

 // sending to all clients in namespace 'myNamespace', include sender
 io.of('myNamespace').emit('message', 'gg');

 // sending to individual socketid
 io.sockets.socket(socketid).emit('message', 'for your eyes only');
{% endhighlight %}

