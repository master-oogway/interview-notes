# JS Interview Notes

## Prototypal vs Classical Inheritance

### Classical Inheritance

```js
function Person(firstname, lastname) {
  this.firstname = firstname;
  this.lastname = lastname;
}

var author = new Person('Aadit', 'Shah');
var author = new Person.apply(null, ['Aadit']); // error
var author = Person.new.apply(Person, ['Aadit', 'Shah']);
```

Problem with Javascript is that since any function can be used as a constructor.

Advantages of `new` keyword over building each object from scratch

1. `new` keyword is cross-platform way to do code reuse
2. `new` is faster than Object creation function and avoids ballooning each object with separate properties for each method.

### Prototypal Inheritance

```js
var object = Object.create(null);
```

```js
var rectangle = {
  area: function() {
    return this.width * this.height;
  }
};

var rect = Object.create(rectangle);
```

```js
/* this is the same thing as above */
var rectangle = Object.create(Object.prototype);

rectangle.area = function() {
  return this.width * this.height;
};

var rect = Object.create(rectangle);
```

`rectangle` is an object literal, which is a succinct way to create a clone of `Object.prototype` and extending it with new properties.

You can manually add `width` and `height` properties to each new `rectangle` clones or you can have a `create` function for rectangle.

```js
// Bad
rect.width = 5;
rect.height = 10;

// Good
var rectangle = {
  create: function() {
    function(width, height) {
      var self = Object.create(this);
      self.height = height;
      self.width = width;
      return self;
    }
  },
  area: function() {
    return this.width * this.height;
  }
};

var rect = rectangle.create(5, 10);
```

Main difference between Constructor Pattern and Prototypal Pattern is that the `new` keyword is not a function so it cannot take advantage of functional programming features.

## `extend` Object function

Object Creation with `extend` function:

```js
Object.prototype.extend = function(extension) {
  var hasOwnProperty = Object.hasOwnProperty;
  var object = Object.create(this);

  for (var property in extension)
    if (hasOwnProperty.call(extension, property) ||
        typeof object[property] === 'undefined')
      object[property] = extension[property];

  return object;
}
```

Extends with Prototypal Inheritance:

```js
var rectangle = {
  create: function(width, height) {
    return this.extend({
      height: height,
      width: width
    });
  },
  area: function() {
    return this.width * this.height;
  }
};

var square = rectangle.extend({
  create: function(side) {
    return rectangle.create.call(this, side, side);
  }
});
```

## Inheriting from multiple prototypes

## Delegation or Differential Inheritance

## Cloning or Concatenative Inheritance

## Event Delegation

Event Delegation refers to how the DOM handles event bubbling or event propagation of ui elements in order to avoid having event handlers for each children DOM element.

Basically you only need to have event handlers on parent nodes and those event handlers will be activated upon interaction with the children nodes.

This saves memory (you only need a few event handlers), and you don't have to rebind existing event listeners to dynamically generated children nodes.

Example:

```js
// Get the element, add a click listener...
document.getElementById("parent-list").addEventListener("click", function(e) {
	// e.target is the clicked element!
	// If it was a list item
	if(e.target && e.target.nodeName == "LI") {
		// List item found!  Output the ID!
		console.log("List item ", e.target.id.replace("post-", ""), " was clicked!");
	}
});
```

## Undefined vs Undeclared variables

**Undeclared** - when a variable does not use `var` keyword.  It's created on global object (`window`) and thus operates in different space as declared variables.

```js
var declaredVariable = 1;

function scopedVariables() {
  undeclaredVariable = 1;
  var declaredVariable = 2;
}

scopedVariables();

undeclaredVariable; // 1
declaredVariable; // 1
```

This creates a lot of confusion, just use `'use strict'` mode.

**Undefined** - Something is undefined the variable is created but no value is set to it.

```js
var undefinedVariable; // undefined
typeof undefinedVariable; // "undefined"

undefinedFunction(); // undefined
typeof undefinedFunction; // "undefined"
```

`undefined` is a primitive type.

You can type check for `undefined` with `typeof(variable) !== "undefined"`.

**Null** - a variable that is defined as `null`.

```js
var nullVariable = null; // null
typeof nullVariable // "object"
```

`null` may be the return value of a function or you explicitly define a variable as `null`.

You can type check for `null` with `variable === null`.

## Closures

** Closure

- the local variable for a function -- kept alive after the function has returned, or
- a stack-frame which is not deallocated when the function returns

Example:

```js
function sayHello2(name) {
    var text = 'Hello ' + name; // Local variable
    var say = function() { console.log(text); }
    return say;
}
var say2 = sayHello2('Bob');
say2(); // logs "Hello Bob"
```

Closures are created when a `function` keyword is inside another `function`.

### Why closures?

1. Closures are used to hide state
2. Used in Classical Object-Oriented JS with constructors
3. Object factories

### More Examples

```js
function say667() {
    // Local variable that ends up within closure
    var num = 666;
    var say = function() { console.log(num); }
    num++;
    return say;
}
var sayNumber = say667();
sayNumber(); // logs 667
```

```js
var gLogNumber, gIncreaseNumber, gSetNumber;
function setupSomeGlobals() {
    // Local variable that ends up within closure
    var num = 666;
    // Store some references to functions as global variables
    gLogNumber = function() { console.log(num); }
    gIncreaseNumber = function() { num++; }
    gSetNumber = function(x) { num = x; }
}

setupSomeGlobals();
gIncreaseNumber();
gLogNumber(); // 667
gSetNumber(5);
gLogNumber(); // 5

var oldLog = gLogNumber;

setupSomeGlobals();
gLogNumber(); // 666

oldLog() // 5
```

Inside functions are recreated if you invoked outside function again.

```js
function buildList(list) {
    var result = [];
    for (var i = 0; i < list.length; i++) {
        var item = 'item' + i;
        result.push( function() {console.log(item + ' ' + list[i])} );
    }
    return result;
}

function testList() {
    var fnlist = buildList([1,2,3]);
    // Using j only to help prevent confusion -- could use i.
    for (var j = 0; j < fnlist.length; j++) {
        fnlist[j]();
    }
}
```

Running `testList()` will result in `item2 undefined` 3 times because there is only one closure for local variables for `buildList`.  They all use the current value of `i` because they share same closure and references.

```js
function newClosure(someNum, someRef) {
    // Local variables that end up within closure
    var num = someNum;
    var anArray = [1,2,3];
    var ref = someRef;
    return function(x) {
        num += x;
        anArray.push(num);
        console.log('num: ' + num +
            '\nanArray ' + anArray.toString() +
            '\nref.someVar ' + ref.someVar);
      }
}
obj = {someVar: 4};
fn1 = newClosure(4, obj);
fn2 = newClosure(5, obj);
fn1(1); // num: 5; anArray: 1,2,3,5; ref.someVar: 4;
fn2(1); // num: 6; anArray: 1,2,3,6; ref.someVar: 4;
obj.someVar++;
fn1(2); // num: 7; anArray: 1,2,3,5,7; ref.someVar: 5;
fn2(2); // num: 8; anArray: 1,2,3,6,8; ref.someVar: 5;
```

There is a closure for each call to a function.

## Anonymous functions

These are functions without names.

Typical use cases:

1. Passing the functions as arguments and defining them on the spot.

```js
setTimout(function() {
  console.log('blah');
}, 1000);
```

2. Array iterables use this a lot

3. IIFE's (Immediately Invoked Function Expressions)

```js
(function() {
  console.log('blah');
})();
```

## The `new` keyword

`new` is considered bad because you can't use it in conjunction with functional features.

## `bind`, `apply`, `call`

## `Object.hasOwnProperty`

## Mixins

## `instanceof` operator

## Why is adding prototype functions to native JS Objects bad?

## Vocabulary

1. Object Literal
2. Ex Nihilo
3. Classical Inheritance
4. Prototypal Inheritance
5. Functional Programming
6. First-class Functions

## Sources

1. [Why Prototypal Inheritance Matters](http://aaditmshah.github.io/why-prototypal-inheritance-matters/)
2. [Advantages of new keyword](http://stackoverflow.com/questions/383402/is-javascripts-new-keyword-considered-harmful/383503#383503)
3. [How Javascript Event Delegation Works](https://davidwalsh.name/event-delegate)
4. [What is Dom Event Delegation](http://stackoverflow.com/questions/1687296/what-is-dom-event-delegation)
5. [Null Undefined Undeclared](http://lucybain.com/blog/2014/null-undefined-undeclared/)
6. [How do Javascript Closures Work?](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)
7. [Why use closures](https://howtonode.org/why-use-closure)
