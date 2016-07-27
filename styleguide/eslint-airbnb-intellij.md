# Airbnb Style Guide in IntelliJ IDEA / WebStorm

Using a standard style guide helps ensure that code is shared easily among developers and any new developer can get up to speed with a given piece of code within no time.
This article covers with ES6 (in Node.js or with Babel.js).

We use the [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript). It is enforced using native IntelliJ-/WebStorm-Features and especially [ESLint](http://eslint.org/). 
Airbnb was chosen because it is both one of the most popular and most comprehensive style guide (source). ESLint was chosen because it has the best ES6 support and is very extensible ([source](http://noeticforce.com/best-javascript-style-guide-for-maintainable-code)).

## Setup

Three components are needed for development: the .eslintrc file, the inspections configuration and the code style configuration. They are all in the ./files folder.

1. Installing dependencies – run the following on a non-sudo command line:

```sh
npm install -g \
  eslint-config-airbnb \
  eslint \
  babel-eslint \
  eslint-plugin-react
```


2. ESLint looks for .eslintrc configuration files up to the root folder. This means that you don't need a file in each project repository, but just in your root projects folder (e.g. your entrecode folder). Download the [eslint file](./files/x.eslintrc), put it into your projects folder and rename it to `.eslintrc` (without the x).
3. Open IntelliJ IDEA / WebStorm and open the global settings (all projects closed!)
4. Copy the [codestyle.xml](./files/codestyle.xml) file to `~/Library/Preferences/<IDE NAME>/codestyles/codestyle.xml`
5. Select Editor -> Code Style and click on 'Manage'. Make sure "airbnb" is selected.
6. Select Editor -> Inspections and click on 'Manage'. Import the [Inspections.xml](./files/Inspections.xml) file. Make sure "airbnb" is selected.
7. Select Languages & Frameworks -> JavaScript -> Code Quality Tools and make sure that only ESLint is enabled. Point to the globally installed ESLint package at `~/.node/lib/node_modules/eslint`
8. Make sure options 4.-6. are also configured correctly for your projects (project settings may overwrite global settings).
9. Make sure you have no conflicting linting or formatting configuration inside your project (extra .eslintrc files, .editorconfig files, jshint configuration in package.json, ...)

## Usage

Your IntelliJ IDEA / WebStorm will highlight issues automatically. By pressing cmd+alt+L your code will get reformatted which solves some (but not all) issues automatically.
When you don't know why an issue is an issue, the first place to look is [https://github.com/airbnb/javascript](https://github.com/airbnb/javascript). It mostly explains the style guide decisions. By searching for an eslint configuration option inside that repository, you will find the setting that airbnb chose. You may then look in [http://eslint.org/](http://eslint.org/) what the option actually does.
While some options are arguable, the airbnb style guide is still a very common best-practice collection and all the options are reasoned in the doc.

> Yep, this is ES6, or better ES2015. But what better time than now to begin using let, const and [all the other](http://es6-features.org/) fancy new JavaScript stuff that is [supported by Node](http://node.green/)? [Here](https://babeljs.io/docs/learn-es2015/) is a nice, short tutorial.
 

## About _ lodash
While [lodash](https://lodash.com/) is still awesome and essential, we should use native alternatives wherever possible for readability, future-proofness and speed.
[Here is a complete list](https://www.reindex.io/blog/you-might-not-need-underscore/), that also includes ES2016 features that are not yet supported by Node.js. Here is a list of the most common lodash functions that became obsolete:
_.each(array, iteratee) → array.forEach(iteratee)
_.map(array, iteratee) → array.map(iteratee) (also works with reduce, reduceRight, every, some, find, indexOf)
_.pluck(array, propertyName) → array.map(value => value[propertyName]) (this is an Arrow Function)
_.compact(array) → array.filter(x => !!x)
see [complete list](https://www.reindex.io/blog/you-might-not-need-underscore/) (all that have ES5.1 or ES2015 should work with node, ES2016 not yet)
Also, when using [async.waterfall](https://github.com/caolan/async#waterfall) or have many callbacks, consider simply using [native Promises](https://promisesaplus.com/). It also has cool native functions like Promise.all([...]).

