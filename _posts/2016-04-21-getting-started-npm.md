---
layout: post
title:  "Getting started with NPM"
date:   2016-04-21 23:08:00
author: kshirish
categories: nodejs
---

## NPM
> Makes it easy to share and reuse the code(packages) that other developers have written.

### Semantic versioning
> A standard to communicate the kind of changes that are there in a particular release.

Generally, a version would be in the form of `Major.Minor.Patch` i.e. `1.0.0`

- Bug fixes and other minor changes: Patch release, increment the last number, e.g. 1.0.1
    - One can specify a patch release for let say `1.0.4` by `1.0`/`1.0.x`/`~1.0.4` 
- New features which don't break existing features: Minor release, increment the middle number, e.g. 1.1.0
    - One can specify a minor release for let say `1.0.4` by `1`/`1.x`/`^1.0.4`
- Changes which break backwards compatibility: Major release, increment the first number, e.g. 2.0.0
    - One can specify a major release for let say `1.0.4` by `*`/`x`

### How does Node look for modules?
For example, if you create a file at `node_modules/foo.js` and then had a program that did `var f = require('foo.js')`, it would load the module. However, `foo.js` is not a *package* in this case, because it does not have a `package.json`.

Alternatively, if you create a package which does not have an `index.js` or a *main* field in the `package.json` file, then it is not a module. Even if it's installed in `node_modules`, it can't be an argument to `require()`.

### Useful Commands
{% highlight shell %}

    # Install a minor release
    npm install underscore@^1.8.3

    # Set versioning of your own package
    npm version <major/minor/patch>

    # Upgrading npm to latest version
    sudo npm install -g npm

    # Generate default package.json
    npm install --yes

    # List all the packages
    npm ls
    npm ls -g --depth=0

    # List outdated packages
    npm outdated
    npm outdated -g --depth=0

    # Update packages
    npm update

    # Uninstall as well as remove entry from package.json
    npm uninstall underscore --save
    npm uninstall -g gulp-cli

    # Delete extraneous packages from your directory
    npm prune

    # Get rid of duplicate nested dependencies
    npm dedupe
{% endhighlight %}

#### References
 - [NPM Docs](https://docs.npmjs.com)