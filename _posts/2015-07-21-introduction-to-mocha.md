---
layout: post
title:  "Introduction to Mocha!"
date:   2015-07-21 12:08:00
categories: js
author: kshirish
---

#What is Mocha?
Mocha is a JavaScript test framework running on Node.js and the browser which makes asynchronous testing smooth.

# Baby step!
{% highlight javascript %}
    describe('Array', function() {  // suite

        describe('#indexOf()', function() { // suite

            it('should not return -1 when the value is present', function() {   // test
                assert.notEqual(-1, [1, 2, 3].indexOf(3));
            });

            it('should return -1 when the value is not present', function() {   // test
                assert.equal(-1, [1, 2, 3].indexOf(5));
                assert.equal(-1, [1, 2, 3].indexOf(0));
            });

        });
    });

{% endhighlight %}

# Diving into async storm! 
{% highlight javascript %}
// it uses `done`, just call it after you think your async will be finished
describe('SetTimeOut', function() {

    it('do not why everyone is scared of me', function(done) {
        setTimeout(function() {
            assert.equal(1, 1);
            done();
        }, 3000);   
        // here timeout might give error, since the default timeout is 2 sec
        // though good news is you can alter it yourself using
        // flag --timeout 13000 or
        // programatically - this.timeout(13000)
    });
});{% endhighlight %}

# Delay the root suite!
{% highlight javascript %}
    setTimeout(function() {

        describe('master of all suites', function() {
            // write your cases here ..    
        });

        run();
        // again use flag --delay to use `run` function.
    }, 3000);

    console.log('Timer begins for 3 seconds atleast');

{% endhighlight %}

# Hooks
{% highlight javascript %}
describe('mocha hooks', function() {

    before('description here', function(){
        // runs before all test in this block
    });

    after('description here', function(){
        // runs after all test in this block
    });

    beforeEach('description here', function(){
        // runs before each test in this block
    });

    afterEach('description here', function(){
        // runs after each test in this block
    });

    // your test cases here ..
});
{% endhighlight %}

# Juggling async Promises
{% highlight javascript %}
// using chai-as-promised
// bad
doSomethingAsync().then(function(result) {
    result.should.equal('foo')
    done();
}, function(err) {
    done(err);
});

// good
return doSomethingAsync().should.eventually.equal('foo');

describe('Promises', function() {

    it('deal with promises', function() {
        // (2+2).should.equal(4)
        // or
        return Promise.resolve(2 + 2).should.eventually.equal(4);
    });
});

{% endhighlight %}

# Dynamically generating test suites
{% highlight javascript %}
// adds only two numbers
function add() {
    return arguments[0] + arguments[1];
}

describe('add()', function() {
    var tests = [{
        args: [1, 21],
        expected: 22
    }, {
        args: [11, 3],
        expected: 14
    }, {
        args: [3, 24],
        expected: 27
    }];

    tests.forEach(function(test) {
        it('correctly adds ' + test.args.length + ' args', function() {
            var res = add.apply(null, test.args);
            assert.equal(res, test.expected);
        });
    });
});
{% endhighlight %}

Check out the [docs](http://mochajs.org/#getting-started)
