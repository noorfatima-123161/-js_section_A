JS Fundamentals Assignment — Section A Answers
A1. Difference between var, let, and const
1. Scope

var is function-scoped. If declared inside a function, it's only available inside that function — but if declared inside a block (like an if or for), it "leaks" outside the block.
let and const are block-scoped. They only exist inside the { } block where they're declared.
function test() {
  if (true) {
    var x = 1;
    let y = 2;
  }
  console.log(x); // 1  (var leaked out of the if-block)
  console.log(y); // ReferenceError (y is block-scoped)
}
2. Hoisting

All three are "hoisted" — meaning JavaScript registers the variable name in memory before the code runs.
var is hoisted and initialized with undefined. So if you access it before the line where it's declared, you get undefined, not an error.
let and const are hoisted but NOT initialized. They sit in a "Temporal Dead Zone" until their declaration line runs.
3. Temporal Dead Zone (TDZ)

TDZ is the time between the start of the block and the line where let/const is actually declared.
During this time, the variable exists (it's hoisted) but cannot be accessed — doing so throws a ReferenceError.
var has no TDZ — it's just undefined until assigned.
Important interview point: let IS hoisted — just hoisted to the TDZ, not to undefined. Saying "let is not hoisted" is technically wrong.
console.log(a); // undefined
console.log(b); // ReferenceError: Cannot access 'b' before initialization
var a = 10;
let b = 20;
4. Re-declaration and Re-assignment

Keyword	Re-declare in same scope?	Re-assign value?
var	Allowed	Allowed
let	SyntaxError	Allowed
const	SyntaxError	TypeError
Note: const doesn't make the value immutable — it makes the binding immutable. You can still mutate properties of a const object/array, just not reassign the variable itself.

5. Which to use in modern JavaScript?

Use const by default — it signals "this binding won't change," making code easier to reason about.
Use let only when you know the variable needs to be reassigned (counters, loop variables, accumulators).
Avoid var entirely — its function-scoping and lack of TDZ cause subtle bugs (especially in loops and closures).
A2. What is the V8 Engine? What does "single-threaded" mean?
1. What is V8? V8 is Google's open-source JavaScript engine, written in C++. It's the engine that powers:

Google Chrome (and other browsers like Edge, Brave)
Node.js — which embeds V8 to run JavaScript outside the browser
2. JIT (Just-In-Time) Compilation In simple terms: instead of slowly interpreting JS code line-by-line every time (like old engines did), V8:

First quickly interprets the code so it starts running fast.
While running, it watches which functions are used a lot ("hot" functions).
It then compiles those hot functions into optimized machine code on the fly, so they run much faster the next time.
This gives JS both fast startup AND fast execution — best of both worlds (interpreter + compiler).

3. What does "single-threaded" mean? JavaScript has one call stack and can do only one thing at a time. There's no running two pieces of JS code simultaneously in the same context — each statement must finish (or yield) before the next one starts.

4. If JS is single-threaded, how does it handle async tasks (setTimeout, fetch)? JS itself doesn't "wait." Instead:

The async operation (timer, network request) is handed off to the Web APIs (browser) or libuv (Node) — which run outside the JS thread.
When that operation finishes, its callback is placed into the Callback Queue (or microtask queue for Promises).
The Event Loop constantly checks: "Is the call stack empty? If yes, take the next item from the queue and push it onto the stack to run."
This is how JS feels like it's doing many things at once, while actually only ever running one piece of JS code at a time.

5. The Pipeline (briefly) Call Stack → executes your synchronous code, one frame at a time Web APIs / libuv → handle timers, I/O, network requests in the background Callback Queue / Microtask Queue → holds completed callbacks waiting to run Event Loop → moves callbacks from the queue to the call stack when it's empty

Follow-up: Is Node.js single-threaded too? The JavaScript execution thread is single-threaded — your JS code runs on one main thread. But Node.js uses libuv, a C++ library with its own thread pool, to handle file system operations, DNS lookups, and some crypto operations in parallel, behind the scenes. So: JS code = single-threaded, but Node's I/O underneath = multi-threaded.

A3. The 8 JavaScript Data Types & Type Coercion
1. The 8 Types

Primitives (7):

Number — e.g. 42, 3.14
String — e.g. "hello"
Boolean — true / false
undefined — a variable declared but not assigned a value
null — represents "intentionally empty"
Symbol — unique, immutable identifier (ES6+)
BigInt — for integers larger than Number can safely hold
Non-Primitive (1): 8. Object — includes plain objects, arrays, functions, dates, etc. (all "object" under the hood)

2. The typeof null === 'object' bug

console.log(typeof null); // 'object'
This is a bug from JavaScript's very first version (1995). null is logically a primitive (it means "no value"), but due to how values were represented internally in early JS, null got tagged with the same internal type code as objects. By the time anyone noticed, too much code already depended on this behavior, so it was never fixed — fixing it would break the web. It remains one of JS's most famous historical quirks.

3. Implicit Coercion (JS converts types automatically, silently)

Example 1 — string + number with +:

console.log("5" + 1); // "51"  (number 1 is converted to string, then concatenated)
Example 2 — using == with different types:

console.log("5" == 5); // true  (string "5" is coerced to number 5 before comparing)
Example 3 — using a value in a boolean context (if condition):

if ("hello") { console.log("truthy!"); } // runs — non-empty string is truthy
4. Explicit Coercion (you intentionally convert types)

Number("123");     // 123        — string to number
String(123);       // "123"      — number to string
Boolean(0);        // false      — number to boolean
Boolean("");       // false      — empty string to boolean
5. Why == is dangerous and === is safe

== (loose equality) coerces types before comparing, leading to confusing results:
console.log(0 == "");        // true
console.log(0 == "0");       // true
console.log("" == "0");      // false  ← inconsistent!
console.log(null == undefined); // true
console.log(false == "0");   // true
=== (strict equality) checks both value AND type — no conversion happens. This makes behavior predictable.
console.log(0 === "");  // false
console.log(0 === 0);   // true
Rule of thumb: always use === and !== unless you have a very specific, well-understood reason to use ==.

A4. Primitive vs Non-Primitive (Reference) Types
1. Primitive Types — stored in the Stack Number, String, Boolean, undefined, null, Symbol, BigInt

These are stored directly in the stack memory (the fast, small memory used for variable storage), as actual values.

2. Non-Primitive Types — stored in the Heap Object, Array, Function, Date, etc.

For these, the actual data lives in the Heap (a larger memory area for dynamic data). The variable in the stack only holds a reference (address/pointer) to where the data lives in the heap.

3. Copying a primitive variable When you copy a primitive, JS copies the actual value. The two variables become completely independent.

let a = 10;
let b = a;  // b gets its own copy of the value 10
b = 20;
console.log(a); // 10 — unaffected
console.log(b); // 20
4. Copying a reference (object/array) variable When you copy an object/array, JS copies the reference (address), NOT the actual data. Both variables now point to the same object in the heap.

let obj1 = { name: "Asad" };
let obj2 = obj1;  // obj2 points to the SAME object as obj1
obj2.name = "Ali";
console.log(obj1.name); // "Ali" — changed too! Same object in memory.
5. Code example: mutation through a copied reference

const original = { score: 100 };
const copy = original;       // copy is just another name for the same object

copy.score = 50;              // mutating through 'copy'

console.log(original.score);  // 50 — original is affected!
Are arrays primitive or non-primitive? Arrays are non-primitive — technically, arrays ARE objects in JS (typeof [] === 'object'). They're stored in the heap and copied by reference, just like any object.

A5. Pass by Value vs Pass by Reference
1. Passing a primitive to a function The function receives a copy of the value. Changes inside the function do NOT affect the original variable.

function changeValue(x) {
  x = 100;
  console.log("inside function:", x); // 100
}

let num = 10;
changeValue(num);
console.log("outside function:", num); // 10 — unchanged
2. Passing an object to a function The function receives a copy of the reference (the address pointing to the object). Both the original variable and the parameter point to the SAME object in the heap.

function changeName(person) {
  person.name = "Changed";
}

const user = { name: "Original" };
changeName(user);
console.log(user.name); // "Changed" — original object was mutated
3. The key nuance: "the reference is passed by value" This means: the address (reference) itself is copied into the function's parameter — like copying a house's address onto a piece of paper. You get your OWN copy of the address, but that address still points to the SAME house.

If you use that address to walk into the house and change something (person.name = "X") → the original house is changed (mutation).
If you write a NEW address on your paper (person = {...} — reassignment) → your paper now points elsewhere, but the original paper (variable) still has the old address.
4. Proof: reassigning obj inside a function does NOT change the original

function reassign(obj) {
  obj = { name: "Brand New Object" }; // obj now points to a NEW object
  console.log("inside:", obj.name);   // "Brand New Object"
}

const original = { name: "Original" };
reassign(original);
console.log("outside:", original.name); // "Original" — unchanged!
5. But mutating obj.property DOES affect the original

function mutate(obj) {
  obj.name = "Mutated"; // changes the property on the SAME object
}

const original2 = { name: "Original" };
mutate(original2);
console.log(original2.name); // "Mutated" — changed!
Summary: JavaScript is technically "pass by value" — but the value being passed, for objects, is a reference (memory address). So: reassignment inside a function never escapes the function, but mutation of properties does affect the original object.

A6. Functions in JavaScript
1. What problem do functions solve? Functions let us wrap reusable blocks of logic so we don't repeat code. Instead of writing the same calculation 10 times, we write it once as a function and "call" it whenever needed — making code shorter, organized, and easier to fix (fix once, works everywhere).

2. Function Declaration Syntax

function functionName(parameter1, parameter2) {
  // code to run
  return result;
}
Example:

function add(a, b) {
  return a + b;
}
3. Is a function declaration hoisted? Can you call it before it's defined? Yes! Function declarations are fully hoisted — both the name AND the function body are moved to the top of their scope. This means you CAN call a function declaration before the line where it's written in the code.

sayHello(); // works! prints "Hello"

function sayHello() {
  console.log("Hello");
}
(Note: this is different from function expressions — const sayHi = function() {...} — which behave like var/let variables and are NOT callable before their assignment line.)

4. Parameter vs Argument

A parameter is the placeholder name listed in the function definition.
An argument is the actual value you pass in when calling the function.
function greet(name) {  // 'name' is the PARAMETER
  console.log("Hi " + name);
}

greet("Asad"); // "Asad" is the ARGUMENT
5. What does a function return if no return statement is written? It returns undefined automatically.

function doNothing() {
  let x = 5; // no return
}
console.log(doNothing()); // undefined
6. Real-world example: validating a user's age

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

console.log(validateAge(25));    // "Age is valid"
console.log(validateAge(15));    // "You must be 18 or older"
console.log(validateAge("20"));  // "Age must be a number"
Bonus note: Functions are also objects in JS! typeof greet === 'function', but greet instanceof Object === true. Functions have properties like .name (the function's name) and .length (number of parameters), because under the hood, a function is just a special, callable object.

