---
layout: post
title:  "Promises ES6!"
date:   2015-07-20 15:16:30
categories: js
---

# Promises in ES6
> `Promises` have been introduced natively as a part of ES6 now. In this blogpost hopefully I will
> try to explore all the aspects of `Promises`.  

## So what is a Promise?
> `Promises` are merely a prettier way of writing code which makes an asynchronous code look like synchronous.

{% highlight javascript %}
// Usual way of XHR 
$.get("script.php", function(data) {
	console.log('Your account has been created successfully!');
});

// With jQuery Promises
var request = $.ajax({
	url: "script.php",
	method: "POST",
	data: {
		'user': formData.username,
		'pwd': formData.password
	}
});

request.done(function(msg) {
	console.log('Your account has been created successfully!');
});

request.fail(function(jqXHR, textStatus) {
	console.log('Looks like the account already exists');
});
{% endhighlight %}

> `Promises` are very much similar to event handling but there are some minor differences.
> A promise can only succeed or fail once.
> If a promise has succeeded or failed and you later add a success/failure callback,
> the correct callback will be called, even though the event took place earlier

> Promises have been around for a while in the form of libraries, 
> like `Q` and `when` which have a standardised behaviour called Promises/A+ same as `ES6 Promises`.
> jQuery also has a Promise type called deferreds but they are not Promises/A+ compliant.

{% highlight javascript %}
var promise = new Promise(function(resolve, reject) {

	// do your async thing
	var req = new XMLHttpRequest();
	req.open('GET', url);

	req.onload = function() {
		if (req.status == 200) {
			resolve(req.response); // resolve here
		} else {
			reject(Error(req.statusText)); // reject here
		}
	};

	req.onerror = function() {
		reject(Error("Network Error")); // reject here
	};

	req.send();
});

promise.then(function(result) { // if Resolved
	console.log(result); // Response
}, function(err) { // if Rejected
	console.log(err); // Error
});
{% endhighlight %}

# Chaining - when you have mutiple Ajax to do.
{% highlight javascript %}
var promise = new Promise(function() {
	if ( /* condition */ ) {
		resolve(1);
	} else {
		reject(-1);
	}
});

promise.then(function(data) {
	data++;
	return data;
}, function(err) {
	err--;
	return err;
}).then(function(data) {
	data++;
	return data;
}, function(err) {
	err--;
	return err;
})
{% endhighlight %}

# Use chaining as middlewares

{% highlight javascript %}
get('story.json').then(function(response) {
	return JSON.parse(response);
}).then(function(response) {
	console.log("Yey JSON!", response);
});

// `then` is smarter than this so if you return a promise
// the next `then` waits for that `promise` to get resolved
getJSON('story.json').then(function(story) {
	return getJSON(story.chapterUrls[0]);
}).then(function(chapter1) {
	console.log("Got chapter 1!", chapter1);
});

// case 1:
get('story.json').then(function(response) {
	console.log("Success!", response);
}, function(error) {
	console.log("Failed!", error);
});

// case 2: Suger for reject: `catch`
get('story.json').then(function(response) {
	console.log("Success!", response);
}).catch(function(error) {
	console.log("Failed!", error);
});

// case 3:
get('story.json').then(function(response) {
	console.log("Success!", response);
}).then(undefined, function(error) {
	console.log("Failed!", error);
});
{% endhighlight %}

> Notice above three cases, only 2nd and 3rd are equivalent.
> If there is an error in `resolve` callback it will go to the next
> `then(undefined, function() {})` and `catch`
> which is not the case in 1st.
> In case 1: only will execute no matter what!

> Check out below example, you will notice one interesting thing
> if for some reason asyncThing1, asyncThing2 or asyncThing3 fails
> then it will go to the immediate `catch` block

{% highlight javascript %}
asyncThing1().then(function() {
	return asyncThing2();
}).then(function() {
	return asyncThing3();
}).catch(function(err) {
	return asyncRecovery1();
}).then(function() {
	return asyncThing4();
}, function(err) {
	return asyncRecovery2();
}).catch(function(err) {
	console.log("Don't worry about it");
}).then(function() {
	console.log("All done!");
});


// Implicit error 
var promise = new Promise(function(resolve, reject) {
	resolve(JSON.parse("This ain't JSON"));
});

promise.then(function(data) {
	console.log("It worked!", data); // expected to come here but
}).catch(function(err) {
	console.log("It failed!", err); // comes here
});

// I think I understood everything, now I can do things on my own.
story.chapterUrls.forEach(function(chapterUrl) {
	// Fetch chapter
	getJSON(chapterUrl).then(function(chapter) {
		// and add it to the page
		addHtmlToPage(chapter.html);
	});
});
{% endhighlight %}

> Not so quick!
> You got to learn the art of `sequence`.

{% highlight javascript %}
// Start off with a promise that always resolves
var sequence = Promise.resolve();

// Loop through our chapter urls
story.chapterUrls.forEach(function(chapterUrl) {
	// Add these actions to the end of the sequence
	sequence = sequence.then(function() {
		return getJSON(chapterUrl);
	}).then(function(chapter) {
		addHtmlToPage(chapter.html);
	});
});
{% endhighlight %}

> Do you smell something wrong with the above approach ?
> What we are doing above? - We are making one AJAX call when the response of 
> previous one is received but we can do much better than that!

> Browsers are pretty good at downloading multiple things at once, 
> so we're losing performance by downloading chapters one after the other. 
> What we want to do is download them all at the same time,
> then process them when they've all arrived.

{% highlight javascript %}
var arrayOfPromises = [
	getPromise('http://api.openweathermap.org/data/2.5/weather?lat=35&lon=139'),
	getPromise('http://api.icndb.com/jokes/random'),
	getPromise('http://api.openweathermap.org/data/2.5/weather?lat=135&lon=19'),
	getPromise('http://api.icndb.com/jokes/random'),
	getPromise('http://api.openweathermap.org/data/2.5/weather?lat=15&lon=29'),
	getPromise('http://api.icndb.com/jokes/random'),
	getPromise('http://api.openweathermap.org/data/2.5/weather?lat=13&lon=90'),
	getPromise('http://api.icndb.com/jokes/random'),
	getPromise('http://api.icndb.com/jokes/random')
];

Promise.all(arrayOfPromises).then(function(arrayOfResults) {

	console.log('All Promises got resolved!'); // All Promises got resolved in same order they were called!
	console.log(arrayOfResults);

}).catch(function(err) {

	console.log('Atleast one of them died on the way!'); // Atleast one of them died on the way!
});


// Description : Whatever resolves/rejects first will be returned
var p1 = new Promise(function(resolve, reject) {

	setTimeout(resolve, 500, "one");
});

var p2 = new Promise(function(resolve, reject) {

	setTimeout(resolve, 100, "two");
});

Promise.race([p1, p2]).then(function(value) {
	console.log(value);
}); // "two"

var p3 = new Promise(function(resolve, reject) {

	setTimeout(resolve, 100, "three");
});

var p4 = new Promise(function(resolve, reject) {

	setTimeout(reject, 500, "four");
});

Promise.race([p3, p4]).then(function(value) {

	console.log(value); // "three"
}, function(reason) {
	// Not called
});

var p5 = new Promise(function(resolve, reject) {

	setTimeout(resolve, 500, "five");
});

var p6 = new Promise(function(resolve, reject) {

	setTimeout(reject, 100, "six");
});

Promise.race([p5, p6]).then(function(value) {
	// Not called              
}, function(reason) {

	console.log(reason); // "six"
});
{% endhighlight %}

# Recipe of the day: Generators and Promises
> Now comes the `Generators`.
> It was not covered in detail in my other post about ES6.
> Let's see what does it bring to the table.

{% highlight javascript %}
var path = 'http://www.html5rocks.com/en/tutorials/es6/promises/';

var asyncFun = function(someUrl, resolve, reject) {

	$.get(someUrl)
		.done(function(response) {

			if (response) {
				resolve(response);
			} else {
				reject('Empty Response');
			}
		})
		.error(function(response) {

			reject('Empty Response');

		});

};

var getPromise = function(someUrl) {

	return new Promise(function(resolve, reject) {

		asyncFun(someUrl, resolve, reject); // do some async

	});
};

var addHtmlToPage = function(html) {

	console.log('Below HTML added to page!');
	console.log(html);
};

function* generatorFun() {

	var path = 'http://www.html5rocks.com/en/tutorials/es6/promises/';
	var stories = yield getPromise(path + 'story.json');

	stories.chapterUrls.forEach(function(chapterUrl) {

		yield getPromise(path + chapterUrl);

	});

}

function runGenerator() {
	var it = generatorFun();

	it.next().then(function(stories) {
		console.log(stories);
		return it.next(stories);
	}).then(function(chapter) {
		console.log(chapter);
		return it.next();
	}).then(function(chapter) {
		console.log(chapter);
		return it.next();
	}).then(function(chapter) {
		console.log(chapter);
		return it.next();
	}).then(function(chapter) {
		console.log(chapter);
		return it.next();
	}).then(function(chapter) {
		console.log(chapter);
	});

}

runGenerator();
{% endhighlight %}

Read more [here](http://www.html5rocks.com/en/tutorials/es6/promises/)
