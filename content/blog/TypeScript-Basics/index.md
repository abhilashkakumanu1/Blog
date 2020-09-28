---
title: "TypeScript Basics"
date: "2020-09-20T22:12:03.284Z"
description: 'Lecture Notes for "TypeScript Course for Beginners" course by maximilian schwarzmüller'
---

This notes is inspired by a [Academind](https://youtu.be/BwuLxPH8IDs) video.
> Typescript is a superset of JavaScript. It's a programming language building up on JavaScript. We can say it takes JavaScript to next level.

- The major disadvantage of TypeScript is that JavaScript environments like Browser, Node.js can't execute it.
- It's a programming language and a tool. It's a powerful compiler to compile TS to JS. 

## Course Outline
1. [Introduction](#introduction)
2. [Working with Types](#working-with-types)


### <a id="introduction"></a>Introduction
Let's understand the benefits of TypeScript by working on this small example.
```javascript
const button = document.querySelector("button");
const input1 = document.getElementById("num1");
const input2 = document.getElementById("num2");

function add(num1, num2){
    return num1+num2;
}

button.addEventListener("click", function(){
    console.log(add(input1.value, input2.value));
})
```
The problem with this code is that, the value of HTML input element will always be string. There won't be any technical error, but there is a logical error because of the way JS is. For example, `add("1", "5")` will output `15` because, the two strings get concatenated. In order to catch these kinds of errors, we can use the following TS code
```typescript
const button = document.querySelector("button");
const input1 = document.getElementById("num1")! as HTMLInputElement;
const input2 = document.getElementById("num2")! as HTMLInputElement;

function add(num1: number, num2: number){
    return num1+num2;
}

button.addEventListener("click", function(){
    console.log(add(+input1.value, +input2.value));
})
```
Here, by using `!` we are saying to the TS compiler that, that element will not be null (i.e, this element exists in the HTML). By using `as HTMLInputElement` we are saying that it's an input element. And finally, `: number` specifies that type.

#### TypeScript Overview
- Types!
- Next gen JS features which then get compiled down for older browsers (kind of like Babel)
- Non-JS features like Interfaces and Generics
- Meta-Programming features like decorators
- Rich Config Options
- Modern tooling that helps even in non-TS projects. For example, VSCode has inbuilt TS features
---

### <a id="working-with-types"></a>Working with Types
> The key difference is: JS uses "dynamic types" (resolved at runtime), TS uses "static types" (set during development)
- number
- string
- boolean
- object
```typescript
function add(num1: number, num2: number){
    return num1+num2;
}
console.log(add("5", 2.5));
```
Even though it gives an error, it goes ahead and compiles it!
__Type Inference__ - TS is pretty good at identifying what type of data a variable belongs to during initialization - `let num1 = 5;`. But it can't infere anything if you just declare it, like this - `let num1;`. So, it's a best practise to declare variables like this - `let num1: number`. 
This is the core job of TS. Checking types and yelling at us if we are using them incorrectly.