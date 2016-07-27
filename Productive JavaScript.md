title: Productive JavaScript
author:
  name: Ruben Deyhle, entrecode GmbH
  email: deyhle@entrecode.de
  twitter: iRuben
  url: https://entrecode.de
output: index.html
style: style.css
controls: true

--

# Productive JavaScript
## ES6 – Promises – Code Quality

--

### Agenda

- ES6/ES2015: notable features
- Promises, done right
- Style Guide & Linting

--

### ES6

- current standard of JavaScript
- ECMAScript 2015
- Full support in Node.js 4/5
- Babel for Frontend Code
- Functional programming features
- Promises

--

### var/const/let

- `var` is no more!
- default replacement is `const`

But what does that mean?

- `const` is for constant: the pointer is immutable. 
    - actual constant if primitive types are used
    - objects and arrays may still change contents
- `let` is for changing or not yet defined types
- both are block-scoped. `var` is not!

--

### Block Scoping Examples

ES5:
```js
if (true) {
  var v = 'v';
}
console.log(v);
```

ES6:
```js
if (true) {
  let l = 'l';
}
console.log(l); // reference error
```

--

### Why `const` and not `let`?
- variables cannot be overwritten by error
```js
const myModule = require('module');
myModule = 'string'; // error
```
- `let` basically only for flags or variables that are not defined at first:

```js
let connected = false;
let connection;

makeConnection((conn) => {
  connected = true;
  connection = conn;
});

```
- **Suggested Workflow:** convert all `var` to `const`, and check where it fails. Put `let` there.

--

### Arrow functions

Mainly syntactic sugar for anonymous functions:

```js
function(parameter) {
  return something;
}
// becomes:
(parameter) => {
  return something
}
// or
(parameter) => something
// or
parameter => something
```

- if you only return something inside the function, `{ }` and `return` can be omitted
- if you have only one parameter and no `{ }`, the `( )` can be omitted, too

-- 

### Why Arrow functions?

- Arrow functions have no own `this` (great if you use `this`) – no `bind(this)` anymore
- more functional programming style
- cleaner callback functions

```js
doSomethingAsync(result => callAnotherFunctionWith(result, andAnotherValue));
```

- if you return a plain object, you can put in in `()`

```js
pairs = evens.map(v => ({ even: v, odd: v + 1 }))
```


- multiple parameters are also ok:

```js
(x, y) => x + y
```

--

### Default function parameters

```js
function f(x, y = 7, z = 42) {
    return x + y + z;
}
```

- do I need to say more?
- only in Node.js >= 6.0
- Babel supports it for Browser use

--

### Rest Parameter

If the exact number of parameters is unknown, we can define an array containing them all:

```js
function f(x, ...a) {
  console.log(x);
  console.log(a);
}
f(1, 2, 3, 4)
```
Logs:

```
1
[2, 3, 4]
```
* only in Node.js >= 4.0 with `--harmony` flag

--

### Spread Operator

- the other way round:

```js
const array = [error, result];
callbackFunction(...array); // callbackFunction is called with (error, result)
```
* only in Node.js >= 4.0 with `--harmony` flag

--

### Template Literals

```js
// Old:
var string = 'text ' + aVariable + ' more text\n'
+ 'in a new line';

// New:
const string = `text ${aVariable} more text
in a new line`;
```

- oh yeah, multi-line strings are here!
- inside can be done anything: 

```js
`the result is: ${val1 + val2 - func(val3)}!`
```

--

### Enhanced Object Properties

ES5:
```js
var something = '';
function f() { ... }

var obj = { 
  something: something, 
  f: f,
  inlineFunction: function() { ... }
};
obj[someOtherVar] = 3;
```

ES6:
```js
const something = '';
const f = () => {};

const obj = { 
  something, 
  f, 
  inlineFunction() { ... }, 
  [someOtherVar]: 3 
};
```
--

### .filter, .map and .forEach

- Not really ES6, but ES5.1... still important.
- no more (for) loops!

```js
array
.filter(key => !!key)
.map(key => key + 1)
.forEach(key => doSomething(key));
```

- filter instead of if
- map if the array should be changed
- forEach if side effects should be triggered
- for Objects:

```js
Object.keys(object)
.forEach(key => doSomething(object[key]));
```

--

### other cool features

- `import` and `export` will replace `require` (not in Node.js yet)
- new Data Types `Map`, `Set`, `WeakMap` and `WeakSet`
    - Map: Key/Value, anything can be a key, replacement for Objects
    - Set: Unique Values, replacement for Arrays (kind of)
- finally an understandable Class notation (Java Style)
- Iterators, Generators
- Promises (in a minute!)
 
--

# Examples
## Or: You might not need lodash

--
### Arrays 1

- `_.each(array, fn)`, `_.map(array, fn)`,  `_.reduce(array, fn)`
    - `array.forEach(fn)`, `array.map(fn)`, `array.reduce(fn)`
- `_.every(array, fn)`, `_.some(array, fn)`,  `_.find(array, fn)`
    - `array.every(fn)`, `array.some(fn)`, `array.find(fn)`
- `_.includes(array, element)`
    - `array.includes(element)` (Node.js ≥ 6.0)

--
### Arrays 2
    
- Remove falsy values: (`_.compact`)
    - `array.filter(x => !!x)`
- Remove duplicates: (`__uniq`)
    - `[...new Set(array)]`
- Find Index with function: (`_.findIndex`)
    - `array.findIndex(fn)`  

Note: these are all array functions. You may need to convert objects to arrays first using Object.keys(obj)

--
### Objects

- Merge objects: (`_.assign`)
    - `Object.assign({}, obj1, { some: thing })` OR
    - `{ ...obj1, { some: thing }}`
- Shallow Clone: `{ ...object }`

--

# Promises
## The key to escape callback hell

--

### O RLY?

```js
datamanager.emailAvailable($scope.registerData.email).then(function (available) {
	if (available) {
		datamanager.getAuthLink('signup', {clientID: ecAuth.clientID}).then(function (url) {
			$http.post(url, {
				email: $scope.registerData.email,
				password: $scope.registerData.password
			}).then(function (registerRes) {
				EcAuthService.login(registerRes.data.token).then(function (user) {
					ecPublicUser.getUser().then(
						function (account) {
							if (account) { //sometimes account is not defined
								if (account.accountID) {
									//do stuff with user
								} else {
									datamanager.logout();
									$location.path('/login');
								}
							} else {
								datamanager.logout();
								$location.path('/login');
							}
					}).catch(console.error.bind(console));
				}).catch(console.error.bind(console));
			});
		}).catch(console.error.bind(console));
	} else {
		$ionicPopup.alert({
			title: 'Fehler',
			template: 'Die von dir gewählte Email-Adresse ist bereits in Benutzung'
		});
	}
 }).catch(console.error.bind(console));
```
--

### Promises

- Well known by Q, Bluebird etc – now a native Object: `Promise`
- Makes a single error callback possible, for Callback Errors and "thrown" errors
- Easy chaining (`async.waterfall` in cool)
- No weird side effects (as Callbacks, which are based on side effects)

--

### Promise Rules

- There are three things that can be done inside a promise handler:
    - `return` another Promise
    - `return undefined` or a synchronous value
    - `throw` an error
- Add 1 `catch`, at the end of the outermost Promise Chain
- Don't use the second parameter of `then` for error handling
- Always return!

--

### Example 1: Promise Hell

Happens if you just use Promises like callbacks:

```js
doSomethingAsync(withParameter).then((function (result) {
  doSomethingElse(result).then(function (innerResult) {
    doThatNow(innerResult).then((finalResult) {
      callback(null, finalResult);
    }).catch(function(error) {
        callback(error);
    }); 
  }).catch(function(error) {
    callback(error);
  })
}).catch(function(error) {
  callback(error);
});
```
(bad!)

--

### Example 1: Promise Hell

Ok, lets put in some structure:

```js
doSomethingAsync(withParameter)
.then((result) => {
  doSomethingElse(result)
})
.then((innerResult) => {
  doThatNow(innerResult);
})
.then(finalResult => callback(null, finalResult))
.catch(callback)
```
(still bad! Why?)


--

### Example 1: Promise Hell

The Promises are not returned!
Best solution:

```js
doSomethingAsync(withParameter)
.then(doSomethingElse)
.then(doThatNow)
.then(finalResult => callback(null, finalResult))
.catch(callback)
```
(good!)

--

### Example 2: forEach

```js
getList(ofThings)
.then((result) => {
  return result.rows.forEach((row) => {
    return db.remove(row.doc);  
  });
})
.then(function () {
  // All deleted! Or not...?
})
.catch(errorHandler);
```
(bad!)


--

### Example 2: forEach

`forEach` returns `undefined`. The second `then` is called immediately. Errors are not handled.
Better:
```js
getList(ofThings)
.then((result) => {
  return Promise.all(result.rows.map((row) => {
    return db.remove(row.doc);  
  });
})
.then(function (weHaveTheResultsHere) {
  // All deleted!
})
.catch(errorHandler);
```
(good! But can be shortened)

--

### Example 2: forEach

`forEach` returns `undefined`. The second `then` is called immediately. Errors are not handled.
Better:
```js
getList(ofThings)
.then(result => Promise.all(
  result.rows.map(row => db.remove(row.doc))
))
.then(function (weHaveTheResultsHere) {
  // All deleted!
})
.catch(errorHandler);
```
(good!)

-- 

### Example 3: Oh no, we have no Promises!

In the old days, Promises were created like that:
```js
function wrapACallbackFunction(name) {
  var deferred = q.defer();

  callingACallbackFunction(function(error, value) {
    if (error) {
      deferred.reject(new Error('ohoh'));
    } else {
      deferred.resolve('Greeting ' + name + ' is not allowed.');
    }
  });
  return deferred.promise;
}
```
(bad!)

--

### Example 3b: Oh no, we have other Promises!

Still often used:
```js
function shouldReturnAPromise(name) {
  var deferred = q.defer();

  doSomethingThatAlsoReturnsAPromise(name).then(function (result) {
    result.addProperty = 'my very own property';
    deferred.resolve(result);
  }
  return deferred.promise;
}
```
(bad!)

--

### Example 3: 

```js
function wrapACallbackFunction(name) {
  return new Promise((resolve, reject) => {
    callingACallbackFunction((error, value) => {
      if (error) {
        return reject(error);
      }
      return resolve(value);
    })
  });
}

// example 3b:
function shouldReturnAPromise(name) {
  return doSomethingThatAlsoReturnsAPromise(name)
  .then((result) => {
    result.addProperty = 'my very own property';
    return result;
  })
}
```
(good!)

--

### Example 4: Sync Start of a Promise Chain

```js
function doSomethingWithJSONString(string) {
  const json = JSON.parse(string);
  json.myOwnProp = anotherObject.key;
  return new Promise((resolve, reject) => {
    resolve(json)
  })
  .then(callSomething)
  .catch(handleError);
}
```
(bad! Why?)

--

### Example 4: Sync Start of a Promise Chain

What if the string is no JSON? JSON.parse will throw.
What if `anotherObject` has no Property `key`? It will throw.

```js
function doSomethingWithJSONString(string) {
  return Promise.resolve(string)
  .then(JSON.parse)
  .then((json) => {
    json.myOwnProp = anotherObject.key;
    return json;
  })
  .then(callSomething)
  .catch(handleError);
}
```
(good!)

--

### Example 4: Sync Start of a Promise Chain

- all Errors are catched
- If you write a function that returns a Promise, it is almost always a good idea to start with `Promise.resolve`;
- There is also `Promise.reject(error)`, but its the same as `throw error` inside a Promise chain.

--

### Example 5: `then(success, error)`

```
somePromise()
.then(() => someOtherPromise())
.catch((error) => {
  // handle error
});

somePromise()
.then(
  () => someOtherPromise(), 
  (error) => {
    // handle error
  }
);
```
Is that the same?

* No!
* Always use `catch`, never the second argument to `then`

--

### Example 6: Promise.all in sequence?

If we want to execute multiple Promises in Parallel, we have Promise.all(promiseArray).
What if we want them executed in sequence?

```js
function executeSequentially(promises) {
  var result = Promise.resolve();
  promises.forEach((promise) => {
    result = result.then(promise);
  });
  return result;
}

const files = ['file1', 'file2', 'file3'];
return executeSequentially(files.map(deleteFile))
.catch(errorHandler);
```
(bad! why?)

--

### Example 6: Promise.all in sequence?

It is still parallel, because the Promises execute immediately when they are created.
Also, executeSequentially returns instantly without waiting for anything.

```js
function executeSequentially(promises) {
  var result = Promise.resolve();
  promises.forEach((promise) => {
    result = result.then(promise);
  });
  return result;
}

const files = ['file1', 'file2', 'file3'];
return executeSequentially(files.map(file => () => deleteFile(file))
.catch(errorHandler);

```
(good!)


--

### Example 6: Promise.all in sequence?

The `executeSequentially` can be further tuned using `reduce`

```js
function executeSequentially(promises) {
  return promises.reduce((result, promise) => result.then(promise), Promise.resolve());
}

const files = ['file1', 'file2', 'file3'];
return executeSequentially(files.map(file => () => deleteFile(file))
.catch(errorHandler);

// OR:

return files
.map(file => () => deleteFile(file))
.reduce((r, p) => r.then(p), Promise.resolve())
.catch(errorHandler);

```

--

### Example 7: Results of 2 Promises

```js
getUserByName('nolan')
.then(user => getUserAccountById(user.id))
.then((userAccount) => {
  return { user, userAccount };
})
.catch(errorHandler);
```
Throws an error.

--

### Example 7: Results of 2 Promises

```js
let user;
getUserByName('nolan')
.then((result) => {
  user = result;
  return getUserAccountById(user.id)
})
.then((userAccount) => {
  return { user, userAccount };
})
.catch(errorHandler);
```
Solution 1 (★★★☆☆)


--

### Example 7: Results of 2 Promises

```js
getUserByName('nolan')
.then((result) => {
  return getUserAccountById(user.id)
  .then((userAccount) => {
    return { user, userAccount };
  })
})
.catch(errorHandler);
```
Solution 2 (★★★★☆)

--

### Example 7: Results of 2 Promises

```js
function addAccountToUserObject(user) {
  return getUserAccountById(user.id)
  .then((userAccount) => {
    return { user, userAccount };
  })
}

getUserByName('nolan')
.then(addAccountToUserObject)
.catch(errorHandler);
```
Solution 3 (★★★★★)

--

### So what about that now?

```js
datamanager.emailAvailable($scope.registerData.email).then(function (available) {
	if (available) {
		datamanager.getAuthLink('signup', {clientID: ecAuth.clientID}).then(function (url) {
			$http.post(url, {
				email: $scope.registerData.email,
				password: $scope.registerData.password
			}).then(function (registerRes) {
				EcAuthService.login(registerRes.data.token).then(function (user) {
					ecPublicUser.getUser().then(
						function (account) {
							if (account) { //sometimes account is not defined
								if (account.accountID) {
									//do stuff with user
								} else {
									datamanager.logout();
									$location.path('/login');
								}
							} else {
								datamanager.logout();
								$location.path('/login');
							}
					}).catch(console.error.bind(console));
				}).catch(console.error.bind(console));
			});
		}).catch(console.error.bind(console));
	} else {
		$ionicPopup.alert({
			title: 'Fehler',
			template: 'Die von dir gewählte Email-Adresse ist bereits in Benutzung'
		});
	}
 }).catch(console.error.bind(console));
```
-- 
### Becomes to this:

```js
datamanager.emailAvailable($scope.registerData.email)
.then((available) => {
  if (!available) {
    return $ionicPopup.alert({
      title: 'Fehler',
      template: 'Die von dir gewählte Email-Adresse ist bereits in Benutzung'
    });
  }
  return datamanager.getAuthLink('signup', {clientID: ecAuth.clientID})
  .then(url => $http.post(url, {
    email: $scope.registerData.email,
    password: $scope.registerData.password
  })
  .then(registerRes => EcAuthService.login(registerRes.data.token))
  .then(user => ecPublicUser.getUser())
  .then((account) => {
    if (account && account.accountID) {
      //do stuff with user
    } else {
      datamanager.logout();
      $location.path('/login');
    }
  })
})
.catch(console.error.bind(console));
```
- one catch
- always return
- early returns instead of else cases
--


# Code Quality
## Style Guide and Linting

**Good News:** IntelliJ/WebStorm knows all about ES6 and helps us using it correctly!
(Preferences, Languages & Frameworks, JavaScript, Language Version)

What we need to configure, is a good Style Guide and automatic Linting.

--

### The Airbnb JavaScript Style Guide

[https://github.com/airbnb/javascript/](https://github.com/airbnb/javascript/)

> A mostly reasonable approach to JavaScript

- can be configured in IntelliJ/Webstorm
- we cover the most important points now

--

### Airbnb JavaScript Style Guide Basics

- Use `const`, avoid `var`. 
- Use single Quotes for Strings
- Use Template Strings if building strings programmatically
- Use Arrow Function Notation for function expressions
- Avoid using Iterators and Generators
- Use `===` and `!==` over `==` and `!=`
- 2 Spaces indentation
- Additional trailing commas in Objects and Arrays
- Use Semicolons
- Don't save references to `this`

--

### ESLint

Simple linting, you only need one configuration file in your root projects folder.

More Doc and how to configure IntelliJ / Webstorm: [Airbnb Style Guide in IntelliJ IDEA / WebStorm](https://github.com/deyhle/talk.productive-javascript/blob/master/styleguide/eslint-airbnb-intellij.md)

-- 

### Resources on ES6

- Short Introduction to ES6: http://es6-features.org/
- ES6 Support in Node.js: http://node.green/
- ES6 in Frontend/Browser Applications: https://babeljs.io/
- You Might Not Need Underscore: https://www.reindex.io/blog/you-might-not-need-underscore/
- Functional Programming in JS: http://cryto.net/~joepie91/blog/2015/05/04/functional-programming-in-javascript-map-filter-reduce/

--

### Resources on Style Guide

- Airbnb JavaScript Style Guide: https://github.com/airbnb/javascript/
- Configuration of Style Guide and ESLint: [Airbnb Style Guide in IntelliJ IDEA / WebStorm](https://github.com/deyhle/talk.productive-javascript/blob/master/styleguide/eslint-airbnb-intellij.md)


--

### Other Resources

- [Creating NPM Modules](https://medium.com/@jdaudier/how-to-create-and-publish-your-first-node-js-module-444e7585b738#.umna0okwi) 
- Using NPM instead of Bower: https://gofore.com/stop-using-bower/
- Git Flow: https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow
- Understanding JSON Schema: https://spacetelescope.github.io/understanding-json-schema/reference/index.html
- DevDocs: http://devdocs.io/
