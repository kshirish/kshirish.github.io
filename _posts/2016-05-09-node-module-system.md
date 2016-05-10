---
layout: post
title:  "Node module system"
date:   2016-05-09 13:08:00
author: kshirish
categories: nodejs
---

## exports vs module.exports
At the very beginning of your module, both of them have a common reference of an empty object.

`exports = module.exports = {}` 

Therefore, consider `exports` like a shortcut/alias which works when
{% highlight javascript %}
    // both module.exoports and exports are still pointing to the same object
    exports.PI = 3.14;
    exports.area = function(radius) {
        return PI * radius * radius;
    };
{% endhighlight %}

And fails when 
{% highlight javascript %}
    // exports has parted its ways with module.exports
    exports = function() {
        console.log('Hi!');
    }
{% endhighlight %}

So just keep in mind that `module.exports` and NOT `exports` will be returned from your module when you requiring that module from somewhere else. Therefore, go for `module.exoprts` and be on the safer side.
 

## module object
> A reference to the object representing the current module.

- `module.children <Array>`: The module objects required by this one.
- `module.loaded <Boolean>`: Whether or not the module is done loading, or is in the process of loading.

## Caching and cycles
Multiple calls to `require('foo')` may not cause the module code to be executed multiple times. This is an important feature. With it, *partially done* objects can be returned, thus allowing transitive dependencies to be loaded even when they would cause cycles.

If you want to have a module execute code multiple times, then export a function, and call that function.

See example:

### a.js
{% highlight javascript %}
    var b = require('./b');

    console.log('upper A');

    module.exports = function() {
        console.log('inside A');
        b();
    };
{% endhighlight %}

### b.js
{% highlight javascript %}
    // In order to prevent an infinite loop, an unfinished copy of 
    // the `a.js`exports object is returned to the `b.js`.
    var a = require('./a');

    console.log('upper B');

    module.exports = function() {
        console.log('inside B');
        a();
    };
{% endhighlight %}

### Output
{% highlight shell %}
    upper B
    upper A
{% endhighlight %}

## Resolve `require()` path

### File Modules
- If the exact filename is not found, then Node.js will attempt to load the required filename with the added extensions: `.js`, `.json`, and finally `.node`
- Without a leading '/', './', or '../' to indicate a file, the module must either be a core module or is loaded from a `node_modules` folder.

### Folder as Modules 
- For example, `require('./some-library')` will try to load `main` entry file in `package.json` if it exists, otherwise 
    - `./some-library/index.js`
    - `./some-library/index.json`
    - `./some-library/index.node`

### Loading from node_modules Folders
- Node.js starts at the parent directory of the current module. If it is not found there, then it moves upwards until the root of the file system is reached.

- For example, if the file at `/home/ry/projects/foo.js` called `require('bar.js')`, then `Node.js` would look in the following locations (in order):

    - `/home/ry/projects/node_modules/bar.js`
    - `/home/ry/node_modules/bar.js`
    - `/home/node_modules/bar.js`
    - `/node_modules/bar.js`

- You can require specific files or sub modules distributed with a module by including a path suffix after the module name. For instance `require('example-module/path/to/file')` would resolve *path/to/file* relative to where *example-module* is located.

### Loading from the global folders
- If the *NODE_PATH* environment variable is set to a colon-delimited list of absolute paths, then `Node.js` will search those paths for modules if they are not found elsewhere.

### References
 - [Official docs](https://nodejs.org/docs/latest/api/modules.html)
 - [Stackoverflow](http://stackoverflow.com/a/26451885/2253756)