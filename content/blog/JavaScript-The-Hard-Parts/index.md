---
title: "JavaScript: The Hard Parts"
date: "2020-09-20T22:12:03.284Z"
description: 'Lecture Notes for "JavaScript: The Parts" course by Will Sentance'
---

There are two halves for JavaScript’s code execution - 
1.  __Thread of Execution__ - The ability to walk through the code line by line by line. Start with line one and do whatever it says to do. Then, after it is done go to line two and do whatever it says to do, etc. It’s executing or doing the code line by line, __it’s threading its way down__ line by line.

2.  __Global Variable Environment__ - Simultaneously the other part that is required to run our code is a place to store the bits of data that we announce as we go down the code
    
These two halves together are called an __execution context__. i.e, context is a space to do something - space in which we execute our code. Whenever we run a function we create a new baby execution context just for that function - local execution context.

## Functions
Function definition - Go save in memory this code and assign it to a value (label). It won’t go into the function definition. Only when the function is called (using parenthesis or special function like call, apply, bind) - the thread of execution goes inside the function definition.

While the function is called, the value of it will be uninitiated (can’t be accessed) until the function is executed. It is not undefined cause undefined is used for the case when the label is not defined. But we have defined the function in this case. So, the correct technical term for it is __uninitiated__. After the return value is returned the execution context corresponding to the function is deleted - garbage collected.

The first thing to happen inside the function execution context is - parameter (the placeholder) is filled with the argument. And after the execution is completed, the function call returns the __value__. The thread of execution weaves in (when the function is called) and out (when it hits the return statement). JavaScript can only do one thing at a time. i.e, to say that it’s __thread is singular__ and __synchronous__ (order top to bottom). As javascript is synchronous, to know where to go back to after executing a particular function, a stack of calls (__call stack__) is used.

## Closures

Functions get new memory every time they are run/invoked. It won’t remember anything from the previous running. And, also what if we also can have permanent memory attached to it. It all starts with us __returning a function__ from the invocation of another function.

> Where you define your functions determine what data it has access to when you call it. When a function is defined, it gets a bond to the surrounding Local Memory (“Variable Environment”) in which it has been defined.

```javascript
function outer (){
 let counter = 0;
 function incrementCounter (){ counter ++; }
 return incrementCounter;
}

const myNewFunction = outer();
myNewFunction();
myNewFunction();
```
We return incrementCounter’s code (function definition) out of outer into
global and give it a new name - myNewFunction
- We maintain the bond to outer’s live local memory - it gets ‘returned out’
attached on the back of incrementCounter’s function definition
- So outer’s local memory is now stored attached to myNewFunction - even
though outer’s execution context is long gone
- When we run myNewFunction in global, it will first look in its own local
memory first (as we’d expect), but then in myNewFunction’s ‘backpack’

Technical term for this backpack is - __Closed Over Variable Environment (C.O.V.E)__ or __Persistent Lexical Scope Referenced Data (P.L.S.R.D)__ or __Closure__ 

- ### Individual backpacks

```javascript
function outer (){
 let counter = 0;
 function incrementCounter (){
 counter ++;
 }
 return incrementCounter;
}

const myNewFunction = outer();
myNewFunction();
myNewFunction();

const anotherFunction = outer();
anotherFunction();
anotherFunction();
```

If we run 'outer' again and store the returned 'incrementCounter' function
definition in 'anotherFunction', this new incrementCounter function was created in a new execution context and therefore has a brand new independent backpack.

- ### Applications
Closure gives our functions persistent memories and entirely new toolkit for writing professional code
- __Helper functions__: Everyday professional helper functions like ‘once’ and ‘memoize’
- __Iterators and generators__: Which use lexical scoping and closure to achieve the most contemporary patterns for handling data in JavaScript
- __Module pattern__: Preserve state for the life of an application without polluting the global namespace
- __Asynchronous JavaScript__: Callbacks and Promises rely on closure to persist state in an asynchronous environment
## Asynchronicity - the backbone of modern web development in javascript
Asynchronicity is going to augment our whole model. The reason we are gonna need to augment our model is that this model is fundamentally untenable in how we think about doing tasks that take a long time. Being synchronous (only go to the next line once you finish this line) what’s gonna happen when we call a server? As we are not allowed to move to the next line, we have to wait for around say 300ms. We have a conundrum - a tension between wanting to __delay some code execution__ but __not wanting to block the thread__ from any further code running while we wait. This leads us to introduce a complementary model as just the above model is not sufficient.

- ### Facade functions a.k.a APIs
The web browser is full of features - DOM, console, local storage (Database thing in the browser), Ability to speak to the internet (XHR feature) - Web browser APIs. They are called APIs cause they are just interfaces to interact with. These are all a facade for functionality that is happening outside the javascript - in the browser.

setTimeOut is not a javascript a function. Its a facade for web browser feature (Timer). It basically starts the timer feature in the web browser. That’s all it does. It spins off a timer feature in the browser. And then it’s done. But when is the code inside the facade function run just after it is completed in the browser? NO! This is because the other function might be running then. Also, the major setback here is in any programming language we need to know when things are gonna happen - not in absolute terms (i.e, not at what time) but in what order. If we don’t know in what order the code is running, how can we write the code accordingly?
- ### Rules 
To make it absolutely certain for us to know the exact order of code execution we need to come up with rules. The rules, in this case, are that all the code inside facade functions (i.e, the asynchronous code), after it gets finished, gets pushed into a queue (__callback queue__ - a queue of functions which are called back, also called as __Macro task queue__). Event loop constants loop through - is the call stack empty? Is there anything in the callback queue? is the call stack empty? Is there anything in the callback queue?.. Only after the entire call stack is empty, the event loop pushes the code in callback queue onto the call stack and they start getting executed in that order. Even though this might not be the best method, this enables us to know the order of execution - which is the only thing that matters.

The problems with this approach are -

1.  __Callback hell__ - The data that comes out of the web browser functionality is passed as an argument to the callback function. So, whatever we have to do with that data can only be done inside the callback function. As the code grows, we need to write more functions and that becomes harder to maintain.
    
2.  Also, maybe it feels a little odd to think of passing a function into another function only for it to run much later.

## Promises
Using __two-pronged__ facade functions that both initiate the background web browser work and return a placeholder object(promise) immediately in Javascript. Let’s consider the example of fetch which is a two-pronged facade function introduced in ES6. Whenever fetch function is called, it has two consequences - start a web browser feature (XHR in this case) and return an object (called promise). This promise object has a __value__ property which is empty for now and will get filled automatically when the background web browser work is finished. Also, this promise object has a hidden property (__onFulfillment__ array) to which we can add functions that are run when the value property gets filled. The value property is given as an argument to these functions. Functions can be added to this array using __.then__ method.

But, we need to know how our promise deferred functionality gets back into javascript. Two-pronged facade functions after they are completed in the background, they are pushed into  the __Micro-task queue__. After all the synchronous code execution is complete, the event loop goes and checks in the micro-task queue first. Only after it is complete, we go into the callback queue. So, the order is Synchronous => microtask queue => callback queue

The event loop never leaves the microtask queue until all the promise based facade functions are executed. i.e, we can starve the callback queue by continuously filling the microtask queue. Promise object has another property called status. It has three values it can have - pending, resolved, rejected. __.catch__ method is used to fill the onRejection list.