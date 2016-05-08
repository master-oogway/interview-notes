# Design Patterns

## Singleton

A Singleton object is an object where only one instance of it exists throughout entire codebase.

There needs to be a `getInstance` method to ensure that any new instantiation will simply point to the same instance all the time.

Example

```js
var Singleton = (function () {
    var instance;

    function createInstance() {
        var object = new Object("I am the instance");
        return object;
    }

    return {
        getInstance: function () {
            if (!instance) {
                instance = createInstance();
            }
            return instance;
        }
    };
})();

function run() {

    var instance1 = Singleton.getInstance();
    var instance2 = Singleton.getInstance();

    alert("Same instance? " + (instance1 === instance2));  
}
```

## Module Pattern

Uses anonymous functions to define closures, to maintain privacy and state throughout the lifetime of an application.

```js
(function () {
	// ... all vars and functions are in this scope only
	// still maintains access to all globals
}());
```

Has global import.

```js
(function ($, YAHOO) {
	// now have access to globals jQuery (as $) and YAHOO in this code
}(jQuery, YAHOO));
```

Has Module Export

```js
var MODULE = (function () {
	var my = {},
		privateVariable = 1;

	function privateMethod() {
		// ...
	}

	my.moduleProperty = 1;
	my.moduleMethod = function () {
		// ...
	};

	return my; // exposing public methods and variables
}());
```

### Loose Augmentation

```js
var MODULE = (function (my) {
	// add capabilities...

	return my;
}(MODULE || {}));
```

### Tight Augmentation

```js
var MODULE = (function (my) {
	var old_moduleMethod = my.moduleMethod;

	my.moduleMethod = function () {
		// method override, has access to old through old_moduleMethod...
	};

	return my;
}(MODULE));
```

### Cloning and Inheritance

```js
var MODULE_TWO = (function (old) {
	var my = {},
		key;

	for (key in old) {
		if (old.hasOwnProperty(key)) {
			my[key] = old[key];
		}
	}

	var super_moduleMethod = old.moduleMethod;
	my.moduleMethod = function () {
		// override method on the clone, access to super through super_moduleMethod
	};

	return my;
}(MODULE));
```

### Cross-File Private state

```js
var MODULE = (function (my) {
	var _private = my._private = my._private || {},
		_seal = my._seal = my._seal || function () {
			delete my._private;
			delete my._seal;
			delete my._unseal;
		},
		_unseal = my._unseal = my._unseal || function () {
			my._private = _private;
			my._seal = _seal;
			my._unseal = _unseal;
		};

	// permanent access to _private, _seal, and _unseal

	return my;
}(MODULE || {}));
```

### Sub-modules

```js
MODULE.sub = (function () {
	var my = {};
	// ...

	return my;
}());
```

The Module Pattern is good for performance.

* It minifies well
* Loose augmentation allows for non-blocking parallel downloads
* Initialization may be slower, but worth tradeoff

## Lazy Load

## Sources

1. [Singleton Design Pattern](http://www.dofactory.com/javascript/singleton-design-pattern)
2. [Javascript Module Pattern in Depth](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html)
