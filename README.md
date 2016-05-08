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

## Sources

1. [Why Prototypal Inheritance Matters](http://aaditmshah.github.io/why-prototypal-inheritance-matters/)
2. [Advantages of new keyword](http://stackoverflow.com/questions/383402/is-javascripts-new-keyword-considered-harmful/383503#383503)
3. [How Javascript Event Delegation Works](https://davidwalsh.name/event-delegate)
4. [What is Dom Event Delegation](http://stackoverflow.com/questions/1687296/what-is-dom-event-delegation)
