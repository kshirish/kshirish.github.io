---
layout: post
title:  "Socketio basics!"
date:   2015-08-06 20:06:35
categories: js
---

### On Client Side

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

### On Server Side

{% highlight javascript %}

    io.on('connection', function(socket) { /** a new socket connects **/ });

{% endhighlight %}

### List all the sockets

{% highlight javascript %}

    io.sockets.sockets  /** array of sockets **/

{% endhighlight %}

### Namespace
> The path of a socket
> default namespace : `/`

{% highlight javascript %}
    
    // emits
    io.emit('some event', 'this is a message');
    // captures
    io.on('connection', function(socket){
    	/** a socket connected **/
    });

{% endhighlight %}

### Custom namespace 
{% highlight javascript %}

    // create a custom namespace on server side
    var customNameSpace = io.of('/my-custom-namespace');
    
    // emits
    customNameSpace.emit('some event', 'this is a message');
    // captures
    customNameSpace.on('connection', function(socket){
    	/** a socket connected **/
    });
{% endhighlight %}

### Connect to a namespace on client side
{% highlight javascript %}

    var socket = io('/my-custom-namespace');
{% endhighlight %}

### Room
> Within each namespace, you can also define channels that sockets can join and leave.

{% highlight javascript %}

    // join a `room`
    io.on('connection', function(socket){
      socket.join('some room');
    });
    
    // leave a `room`
    io.on('connection', function(socket){
      socket.join('some room');
    });
    
    // broadcast to entire `room`
    io.to('some room').emit('some event');
    // or
    io.in('some room').emit('some event');
{% endhighlight %}

### The Default room
> Each Socket in Socket.IO is identified by a unique identifier Socket#id
and automatically joins a room identified by this id.

### Debugging

> On client side (browsers)

{% highlight javascript %}

    localStorage.debug = '*';
{% endhighlight %}

> On server side (node)

{% highlight javascript %}

    $ DEBUG=* node yourfile.js
{% endhighlight %}

### Cheatsheet
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










