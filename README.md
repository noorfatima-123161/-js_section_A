A1. What is the Difference Between var, let, and const in JavaScript?

JavaScript provides three keywords for declaring variables: var, let, and const. Although all three are used to store data, they differ in terms of scope, hoisting, reassignment, and redeclaration rules.

1. Scope

Scope determines where a variable can be accessed in a program.

var

var is function-scoped. If it is declared inside a function, it can be accessed anywhere within that function. However, if declared inside a block such as an if statement or loop, it can still be accessed outside that block.

function test() {
    if (true) {
        var x = 10;
    }
    console.log(x); // 10
}
let and const

let and const are block-scoped. They can only be accessed inside the block in which they are declared.

function test() {
    if (true) {
        let y = 20;
        const z = 30;
    }

    console.log(y); // Error
    console.log(z); // Error
}
2. Hoisting

Hoisting is JavaScript's behavior of moving declarations to the top of their scope before execution.

var Hoisting
console.log(a);
var a = 10;

Output:

undefined

The variable is hoisted and automatically initialized with undefined.

let and const Hoisting
console.log(b);
let b = 20;

Output:

ReferenceError

let and const are also hoisted, but they are not initialized immediately.

Important Interview Question

Is let hoisted?

Yes. Many people incorrectly say "No."

The correct answer is:

let and const are hoisted, but they remain in the Temporal Dead Zone (TDZ) until execution reaches their declaration.

3. Temporal Dead Zone (TDZ)

The TDZ is the period between entering a scope and the actual declaration of a let or const variable.

During this period, accessing the variable causes a ReferenceError.

{
    console.log(x);
    let x = 10;
}

Output:

ReferenceError

var does not have a TDZ because it is initialized with undefined.

4. Re-declaration and Re-assignment
Keyword	Re-declaration	Re-assignment
var	Allowed	Allowed
let	Not Allowed	Allowed
const	Not Allowed	Not Allowed
Example
var a = 10;
var a = 20; // Allowed

let b = 10;
b = 20; // Allowed

const c = 10;
c = 20; // Error
5. Which One Should Be Used?

Modern JavaScript developers follow these rules:

Use const by default
const pi = 3.14;

It prevents accidental reassignment and makes code easier to understand.

Use let when the value changes
let count = 0;
count++;
Avoid var

var can cause unexpected bugs because of function scope and hoisting behavior.

Conclusion
var is function-scoped, hoisted with undefined, and allows redeclaration.
let is block-scoped, hoisted into the TDZ, and allows reassignment but not redeclaration.
const is block-scoped, hoisted into the TDZ, and does not allow reassignment.
Modern JavaScript primarily uses const and let, while var is generally avoided.
A2. What is the V8 Engine? What Does It Mean That JavaScript Is Single-Threaded?
1. What is V8?

V8 is Google's open-source JavaScript engine written in C++.

It is responsible for executing JavaScript code.

Environments That Use V8
Google Chrome
Microsoft Edge
Brave Browser
Opera Browser
Node.js

Without V8, JavaScript code would not run in these environments.

2. What is JIT (Just-In-Time) Compilation?

Older JavaScript engines interpreted code line by line.

V8 uses Just-In-Time (JIT) Compilation.

How It Works
JavaScript code is read.
V8 quickly interprets the code.
Frequently used functions are identified.
Those functions are converted into optimized machine code.

This allows JavaScript to start quickly and run efficiently.

Example
function add(a, b) {
    return a + b;
}

for(let i = 0; i < 1000000; i++) {
    add(5, 10);
}

Since the function runs many times, V8 optimizes it into machine code.

3. What Does Single-Threaded Mean?

JavaScript is called single-threaded because it has only one call stack.

This means JavaScript can execute only one task at a time.

Example
console.log("First");
console.log("Second");
console.log("Third");

Output:

First
Second
Third

Each statement must finish before the next one starts.

4. If JavaScript Is Single-Threaded, How Does It Handle Async Tasks?

JavaScript uses the Event Loop architecture.

Components
Call Stack

Executes JavaScript code.

Web APIs

Provided by browsers for handling timers, network requests, DOM events, etc.

Callback Queue

Stores completed callbacks waiting to execute.

Event Loop

Checks whether the Call Stack is empty and moves callbacks into it.

Example
console.log("Start");

setTimeout(() => {
    console.log("Timer Done");
}, 2000);

console.log("End");

Output:

Start
End
Timer Done
Why?
"Start" is printed.
setTimeout is sent to Web APIs.
"End" is printed.
After 2 seconds, callback enters Callback Queue.
Event Loop moves it to Call Stack.
"Timer Done" is printed.
5. JavaScript Execution Flow
Call Stack
      ↓
Web APIs
      ↓
Callback Queue
      ↓
Event Loop
      ↓
Call Stack

This process enables asynchronous programming.

6. Is Node.js Also Single-Threaded?

This is a common interview question.

Answer

JavaScript execution inside Node.js is single-threaded because it uses one Call Stack.

However, Node.js uses libuv, a C++ library that provides a multi-threaded thread pool for handling:

File system operations
Network requests
DNS lookups
Cryptography operations

Therefore:

JavaScript code → Single-threaded
Underlying I/O operations → Multi-threaded through libuv
A3. Explain the 8 JavaScript Data Types. What is Type Coercion – Implicit vs Explicit?

JavaScript is a dynamically typed language, which means variables can hold different types of values without explicitly specifying their data type. JavaScript has 8 data types, consisting of 7 primitive types and 1 non-primitive type.

1. The 8 JavaScript Data Types
Primitive Data Types (7)

Primitive data types store a single value and are immutable.

1. Number

Used for integers and floating-point numbers.

let age = 20;
let price = 99.99;
2. String

Used to store text.

let name = "Ali";
3. Boolean

Represents true or false values.

let isLoggedIn = true;
4. Undefined

A variable declared but not assigned a value.

let x;
console.log(x); // undefined
5. Null

Represents an intentionally empty value.

let user = null;
6. Symbol

Used to create unique identifiers.

let id = Symbol("id");
7. BigInt

Used for very large integers beyond the safe limit of Number.

let bigNumber = 12345678901234567890n;
Non-Primitive Data Type (1)
8. Object

Objects store collections of data.

let person = {
    name: "Ali",
    age: 22
};

Arrays, functions, and dates are also objects in JavaScript.

2. The Famous typeof null === "object" Bug
console.log(typeof null);

Output:

"object"

This is a historical bug from JavaScript's first version in 1995.

Although null is a primitive value representing "no value," it was mistakenly assigned the internal type code used for objects. By the time this issue was discovered, many websites relied on this behavior, so it was never fixed to maintain backward compatibility.

3. Type Coercion

Type coercion is the automatic or manual conversion of one data type into another.

There are two types:

Implicit Coercion

JavaScript automatically converts data types when needed.

Example 1
console.log("5" + 1);

Output:

"51"

The number 1 is converted into a string and concatenated.

Example 2
console.log("5" == 5);

Output:

true

JavaScript converts "5" into the number 5 before comparison.

Example 3
if ("Hello") {
    console.log("Truthy");
}

Output:

Truthy

A non-empty string is automatically converted to true.

4. Explicit Coercion

Explicit coercion occurs when the programmer intentionally converts a value.

Number()
Number("123");

Output:

123
String()
String(123);

Output:

"123"
Boolean()
Boolean(1);

Output:

true
5. Why == Is Dangerous and === Is Safe
Loose Equality (==)

== performs type coercion before comparison.

console.log(0 == "");

Output:

true
console.log(false == "0");

Output:

true

These results can be confusing.

Strict Equality (===)

=== compares both value and data type.

console.log(0 === "");

Output:

false
console.log(5 === 5);

Output:

true
Best Practice

Always use === and !== unless there is a specific reason to use ==.

Conclusion

JavaScript has eight data types: seven primitive types and one non-primitive type. Type coercion can occur automatically (implicit) or manually (explicit). Developers should understand coercion behavior and prefer strict equality (===) for predictable results.

A4. Primitive vs Non-Primitive Data Types in JavaScript

JavaScript data types are divided into two categories: Primitive and Non-Primitive (Reference) types. They differ in how they are stored in memory and copied.

1. Primitive Data Types

Primitive types include:

Number
String
Boolean
Undefined
Null
Symbol
BigInt
Storage

Primitive values are stored directly in Stack Memory.

let age = 20;

The actual value 20 is stored directly in memory.

2. Non-Primitive Data Types

Non-primitive types include:

Object
Array
Function
Date
Storage

These are stored in Heap Memory.

The variable stores only a reference (memory address) to the object.

let person = {
    name: "Ali"
};
3. Copying Primitive Values

When a primitive value is copied, the actual value is duplicated.

let a = 10;
let b = a;

b = 20;

console.log(a); // 10
console.log(b); // 20

Changing b does not affect a.

4. Copying Reference Values

When an object or array is copied, only the reference is copied.

let obj1 = {
    name: "Ali"
};

let obj2 = obj1;

obj2.name = "Ahmed";

console.log(obj1.name);

Output:

Ahmed

Both variables point to the same object.

5. Mutation Example
const original = {
    score: 100
};

const copy = original;

copy.score = 50;

console.log(original.score);

Output:

50

The original object changes because both variables reference the same memory location.

Interview Question
Are Arrays Primitive or Non-Primitive?

Arrays are non-primitive because arrays are objects in JavaScript.

console.log(typeof []);

Output:

object
Conclusion

Primitive values are stored in the stack and copied by value. Non-primitive values are stored in the heap and copied by reference. Understanding this difference helps prevent unexpected behavior when working with objects and arrays.

A5. Pass By Value vs Pass By Reference

Pass by value and pass by reference describe how data is passed into functions.

1. Pass By Value

When a primitive value is passed to a function, a copy of the value is passed.

function changeValue(x) {
    x = 100;
}

let num = 10;

changeValue(num);

console.log(num);

Output:

10

The original variable remains unchanged.

2. Passing Objects

Objects behave differently because a reference is passed.

function changeName(person) {
    person.name = "Ahmed";
}

const user = {
    name: "Ali"
};

changeName(user);

console.log(user.name);

Output:

Ahmed

The original object is modified.

3. The Important Concept: Reference Passed By Value

JavaScript is technically pass by value.

For objects, the value being copied is the memory address (reference).

Therefore:

The reference is copied.
Both variables point to the same object.
4. Reassigning Does NOT Affect Original
function reassign(obj) {
    obj = {
        name: "New Object"
    };
}

const original = {
    name: "Ali"
};

reassign(original);

console.log(original.name);

Output:

Ali

The original object remains unchanged.

5. Mutating DOES Affect Original
function mutate(obj) {
    obj.name = "Changed";
}

const user = {
    name: "Ali"
};

mutate(user);

console.log(user.name);

Output:

Changed

Because both references point to the same object.

Conclusion

JavaScript is not truly pass-by-reference. It is pass-by-value, but when objects are passed, the value copied is the object's reference. Therefore, property mutations affect the original object, while reassignment does not.

A6. Functions in JavaScript

A function is a reusable block of code designed to perform a specific task. Functions help avoid code repetition and make programs more organized and maintainable.

1. What Problem Do Functions Solve?

Without functions, the same code would need to be written multiple times.

Functions promote:

Code reusability
Better organization
Easier maintenance
Reduced duplication
2. Function Declaration Syntax
function functionName(parameter1, parameter2) {
    return result;
}
Example
function add(a, b) {
    return a + b;
}
3. Function Hoisting

Function declarations are fully hoisted.

This means they can be called before their definition.

sayHello();

function sayHello() {
    console.log("Hello");
}

Output:

Hello
4. Parameter vs Argument
Parameter

A parameter is a variable listed in the function definition.

function greet(name) {
}

name is a parameter.

Argument

An argument is the actual value passed during function call.

greet("Ali");

"Ali" is an argument.

5. Return Values

If no return statement is written, JavaScript automatically returns undefined.

function test() {
    let x = 10;
}

console.log(test());

Output:

undefined
6. Real-World Example: Age Validation
function validateAge(age) {
    if (typeof age !== "number") {
        return "Age must be a number";
    }

    if (age < 18) {
        return "You must be 18 or older";
    }

    if (age > 120) {
        return "Please enter a valid age";
    }

    return "Age is valid";
}

console.log(validateAge(25));
console.log(validateAge(15));
console.log(validateAge("20"));

Output:

Age is valid
You must be 18 or older
Age must be a number
7. Functions Are Also Objects

Functions are special objects in JavaScript.

function greet() {}

console.log(typeof greet);

Output:

function

Functions also have properties:

console.log(greet.name);
console.log(greet.length);

Functions are objects because:

console.log(greet instanceof Object);

Output:

true
Conclusion

Functions are reusable blocks of code that improve organization and reduce repetition. Function declarations are hoisted, can accept parameters and arguments, and return values. In JavaScript, functions are also objects with their own properties and methods.