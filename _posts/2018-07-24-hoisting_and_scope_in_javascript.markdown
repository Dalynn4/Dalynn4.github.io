---
layout: post
title:      "Hoisting and Scope in Javascript"
date:       2018-07-25 00:10:16 +0000
permalink:  hoisting_and_scope_in_javascript
---


Today, we are going to go over scope and hoisting in Javascript(JS). We will start with scope and MDN's base definition of it:

```
Scope is the current context of execution. The context in which values and expressions are "visible," or can be referenced. If a variable or other expression is not "in the current scope," then it is unavailable for use.
```

As you start writing code in a new file, you would be in what is known as the global scope. Anything declared in this top-most scope is available to all other declarations in that file. Most of the coding you will do, however, will be inside of declared functions. Whenever you declare a new function, you are creating a new scope. That's because, in JS, functions have their own scope, which means anything declared inside that function will only be available to that particular function. Let's take a look at a simple example.

```
function myFunc () {
  const myVar = 42;
 
  return myVar * 2;
}
// => undefined
 
myFunc();
// => 84
```
When we declare a new function and write some code in the function body, we're no longer in the global scope. The function creates its own scope. Inside the function body, we can reference variables and functions declared in the function's scope. However, from outside the function, we can't reference anything declared inside of it:

```
function myFunc () {
  const myVar = 42;
}
// => undefined
 
myVar * 2;
// Uncaught ReferenceError: myVar is not defined
```

When referencing variables and functions in your code, it is always important to realize what scope you are currently in, and what scope the variable or function you are using was defined in.

### Do you even ...hoist, bro?

To talk about hoisting, we must go over how the JS engine goes over our JS code. JS actually makes two separate passes over our code when going through it. The first pass is the compilation phase, in which the engine steps through our code line-by-line. When it reaches a variable declaration, the engine allocates memory and sets up a reference to the variable's identifier, `const myVar =`. 
When the engine encounters a function declaration, it does three things
-Allocates memory and sets up a reference to the function's identifier, `myFunc = ()`
-Creates a new execution context with a new scope.
-Adds a reference to the parent scope (the outer environment) to the scope chain, making variables and functions declared in the outer environment available in the new function's scope.

The second pass is the execution phase. The JS engine again steps through our code line-by-line, but this time it actually runs our code, assigning values to variables and invoking functions.

While it makes sense to define a function before we invoke it:
```
function myFunc () {
  return 'Hello, world!';
}
 
myFunc();
// => "Hello, world!"
```
Because of the way JS reads our code, doing that in reverse will function the same:
```
myFunc();
 
function myFunc () {
  return 'Hello, world!';
}
// => "Hello, world!"
```
All this is because of the dual pass that the JS engine makes. In the second example, JS runs over our code the first time, setting up our function, but passing over our invocation of it. When it passes over it the second time, the function is invoked, and because JS knows what it is(because of it being declared in the first pass), it will execute just fine.

All of this leads us to the idea of hoisting. Hoisting is termed such because of the idea that your declarations are being *hoisted* to the top of your current scope. Your declarations *are* being evaluated before the rest of your code gets run, but thats only because of the way that JS runs your code. Lets take a look at an example:

```
function myFunc () {
  console.log(hello);
 
  var hello = 'World!';
}
```
Here, when we run this function, JS will know that `hello` has been declared, but at the point that we are trying to log it, it has not been assigned. We instead just log undefined. Here is the same function rearranged to better indicate the order of events:
```
function myFunc () {
  var hello;
 
  console.log(hello);
 
  hello = 'World!';
}
```
As you can see, it is always preferable to declare and assign variables at the same time, at the top of your current scope.

#### Let and Const

With the new versions of JS that have been released in the last couple of years, a new way to declare and assign variables was introduced. `let` and `const` are now the preferred way to work with variables. We use `const` in most cases, unless we are certain the value of the variable will change, as `const` cannot be reassigned. In those cases, we use `let`. Variables declared with `const` and `let` do technically get 'hoisted', but the JavaScript engine doesn't allow them to be referenced before they've been initialized. Let's look at an example:
```
function do_something() {
  console.log(bar); // undefined
  console.log(foo); // ReferenceError
  var bar = 1;
  let foo = 2;
}
```
and here it is after 'hoisting:
```
function do_something() {
 var bar;
  let foo;
  console.log(bar); // undefined
  console.log(foo); // ReferenceError
  bar = 1;
  foo = 2;
}
```
Unlike `var`, `let` and `const` will actually throw an error if the hoisting of the variable messes up your function.
Hoisting is an easy to understand, but often overlooked nuance of the JS language. Without a proper understanding of hoisting, your programs are susceptible to subtle bugs. Keep in mind to always declare and assign your variables at the top of your scope, and you should be just fine!
