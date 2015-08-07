---
layout: post
title:  "10 minutes with Firebase"
date:   2015-08-07 20:36:15
categories: js
---

Firebase is available as both node module and browser side script.


### Every data has a url 

{% highlight javascript %}

    var ref = new Firebase('https://test231.firebaseio.com/');
    rootRef = new Firebase('https://test231.firebaseio.com/');
        
    usersRef = rootRef.child('users');
    // OR
    usersRef = new Firebase("https://test231.firebaseio.com/users");
        
    // access a specific data by url
    ageRef = new Firebase("https://test231.firebaseio.com/users/mohan/age");

{% endhighlight %}


### Write(save) data

{% highlight javascript %}

    // set the age of mohan to 44
    ageRef.set(44);
    mohanRef = usersRef.child('mohan');
    mohanRef.set({ full_name: 'Mohan Lal', age: 50, city: 'Bengaluru' });

{% endhighlight %}


### Update data

> `update()` only updates data at the first child level, any data passed in beyond the first child level is a treated as a `set()` operation.

{% highlight javascript %}

    mohanRef.update({ job: 'WeeWee' });
    // { full_name: 'Mohan Lal', age: 50, city: 'Bengaluru', job: 'WeeWee' }

{% endhighlight %}


###  Write and Update using callbacks

{% highlight javascript %}

    someRef.set('this is a message', function( err ) {
         if(!err) {
              console.log('Data was updated successfully!');
         }
    });

{% endhighlight %}


### Push data

>  At a multi user platform where simultaneously several users are engaging with the same data, the use of `set()` might be hazardous. Instead firebase provides `push()` that generates a unique ID every time a new child is added to the specified database reference.


{% highlight javascript %}

        var postsRef = ref.child("posts");

        // `push()` returns the ref to the newly added data 
        var refMohanPost = postsRef.push({
             author: "mohan",
             title: "How to swim in an empty pond"
        });

        var refLilyPost = postsRef.push({
            author: "lily",
             title: "You smell like pie"
        });

        // get the unique ID generated
        var mohanPostId = refMohanPost.key();

{% endhighlight %}


### Transaction

> These are useful for "atomic operations"

{% highlight javascript %}
    // Atomically incrementing an upvote

    var upvoteRef = new Firebase(' ... upvote data url ... ');
    upvoteRef.transaction(function (current_value) {
         return (current_value || 0) + 1;
    });


    // Try to create a user for mohan, but only if the user id 'mohan' isn't already taken

    var mohanRef = new Firebase('https://test231.firebaseio.com/users/mohan');
    mohanRef.transaction(function(currentData) {
 
    if (currentData === null) {
             console.log('User mohan does not exist therefore we can create it.');
             return { full_name: 'Mohan Lal', age: 50, city: 'Bengaluru' };
         } else {
             console.log('User mohan already exists.');
             return; // Abort the transaction i.e. Don't return anything
         }
    }, function(error, committed, snapshot) {
     
          if (error) {
             console.log('Transaction failed abnormally!', error);
         } else if (!committed) {
             console.log('We aborted the transaction (because mohan already exists).');
         } else {
             console.log('User mohan added!');
         }

         console.log("Wilma's data: ", snapshot.val());

    });

{% endhighlight %}


### Offline

> A firebase client also maintains its own local version of it, so everytime it is written to this local version first and then client then synchronizes the local version with the database. Once connectivity is reestablished, firebase database will receive the appropriate set of events so that the client "catches up" with the current server state.


### Read data

{% highlight javascript %}

    usersRef = new Firebase("https://test231.firebaseio.com/users");

    // returns the entire users array 
    usersRef.on('value', function(snapshot) {
         console.log( snapshot.val() );  // array
    }, function(err) {
         console.log('Looks like you do not have permission to READ.');
    });

    // returns the added `child` 
    usersRef.on('child_added', function(snapshot) {
         console.log( snapshot.val() );  // array element
    }, function(err) {
         console.log('Looks like you do not have permission to READ.');
    });

{% endhighlight %}

And similarly, `child_changed` and `child_removed`.


### once()

{% highlight javascript %}

    firebaseRef.once('value', function (snapshot) {
        // code to handle new value
    }, function (err) {
        // code to handle read error
    });

{% endhighlight %}


### Security Check!

> Below example allows anyone to read/write `/users/mohan` and its children, but doesn't allow for admin.

#### Config

{% highlight javascript %}

    {
         "rules": {
             "users": {
                 "admin": {
                     ".read": false,
                     ".write": false
                 },
                 "mohan": {
                     ".read": true,
                     ".write": true
                  }
             }
         }
    }

{% endhighlight %}


#### Authenticate with a token

{% highlight javascript %}

    usersRef = new Firebase("https://test231.firebaseio.com/users");
    usersRef.authWithCustomToken("MY_AUTH_TOKEN", function(error, authData) {
        if (error) {
            console.log("Authentication Failed!", error);
        } else {
            console.log("Authenticated successfully with payload:", authData);
        }
    });

{% endhighlight %}


### How to check if user is authenticated or not?

{% highlight javascript %}

    var authData = usersRef.getAuth();
    if (authData) {
         console.log("Authenticated!");
    }

{% endhighlight %}


### A few pieces of API

{% highlight javascript %}

    // Parent
    var parentRef = usersRef.parent();   // https://test231.firebaseio.com

    // Root
    var rootRef = usersRef.root();   // https://test231.firebaseio.com

{% endhighlight %}

#### Check out more 

- (Docs)[https://www.firebase.com/docs/]
- (API)[https://www.firebase.com/docs/web/api/]


