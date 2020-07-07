# Javascript (ES5)

## Basics

Javascript is a Object Oriented language that has inheritance via prototypes.

There are 5 primitive types (as of ES5): 
* `number`, 
* `string`, 
* `boolean`, 
* `null`, 
* `undefined`

## Objects 

Everything else besides primitive types in JavaScript are Objects. Which means they can have properties and methods (which also known as Function Objects). 

Object creation is done using the shortcut constructor of `{}` or use the regular constructor of `new Object()`.

You can then add new properties or methods to the Object as shown below.

```javascript
var myObject = {};
myObject.Name = 'Wes'; // Add a property of Name
myObject.helloWorld = function() { console.log('Hello World ' + this.Name) }; // Add a method to the object
myObject.helloWorld(); // Console outputs "Hello World Wes"
```

The `this` syntax keyword allows you to reference properties off the Object in context.

Each additional property we add to the Object create a key value pair that is used to reference the property. The key is the name of the property and the value is what was assigned to the key, either an Object, primitive, or a function (in which case, the property is actually a method, which is really a Function Object). 

> "Objects are just collections of named properties, a list of key-value pairs." (Stoyan Stefanov, *Javascript Patterns*)

This is demonstrated by using the square brackets:

```javascript
myObject['helloWorld'](); // Executes the function and outputs "Hello World Wes"
myObject['Name']; // Returns "Wes"
```

There are two types of Objects:
1. Native Objects - Built in JavaScript Objects (From ECMAScript) or User Defined
    1. Built in Objects examples are Array, Date, etc...
    2. User made Objects such as myObject above.
2. Host Objects - Objects defined by your environment, either the Browser or Node.js, or others. `window` or `console` are both examples of these.

### Prototypes

All objects have the  `prototype` object on them. Inheritance in JavaScript uses the `prototype` to add new properties and methods to an Object and allow children to inherit and use them also.

The `prototype` chain is live, so when adding or removing members all other Objects also get updated.

It is not recommend to modify the `prototype` of built in Objects since that affects them globally and could cause unexpected errors elsewhere in the program. If you are importing libraries or scripts they might error due to unexpected changes you have added.

> This is how Polyfills work. They modify the `prototype` of the built in Objects to add new functionality. This is how we can get `.map()` for Arrays from ES5 ported to earlier versions. See more [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map#Polyfill).

#### Context, `this`, and Function Objects

As mentioned previously, methods are actually considered [Function Objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function), so methods themselves also have members such as `prototypes` and properties. This is why you can call a function the following way:

```javascript
myObject.helloWorld.call() // Executes and outputs "Hello World undefined"
```

Notice that "Wes" was lost from the logging output and now returns undefined. This was due to a loss of context when invoking the function through `.call()`. Context is important in JavaScript as the interpreter uses context to determine what code to execute. Since the line of code above was called without context provided, it could not know what `this.Name` was in the `helloWorld()` method. By default `.call()` will use the Global Context unless we give it the context by passing our Object in as the first parameter.
 
```javascript
myObject.helloWorld.call(myObject) // Executes and outputs "Hello World Wes"
```

You would not typically execute code this way, but it is important to understand that context does matter when using the `this` keyword and frequently pops up when using Objects. When invoking a method normally the context is already included, similar to Java or C# instance methods. More about `this` can here learned [here](https://blog.kevinchisholm.com/javascript/context-object-literals/#h3).
