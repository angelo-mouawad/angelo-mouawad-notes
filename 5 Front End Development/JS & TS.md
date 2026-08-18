# What Is Java Script ?

**JavaScript (JS)** is a **programming language** primarily used to make websites **interactive**.
- It can run **inside a browser** like Chrome, Firefox, and Safari to control webpages.
- It can also run **outside the browser** using **Node.js**.

Node is the **JavaScript runtime environment** that allows you to run JavaScript **outside of a web browser**. You can download Node from the [node.js](https://nodejs.org/en) website. The Node package manager is `npm`.

# What Is Type Script ?

**TypeScript (TS)** is a **programming language** that is a **superset of JavaScript**.
- It is developed and maintained by **Microsoft**.
- TypeScript adds **static typing** and other features to JavaScript.
- Any valid **JavaScript code is also valid TypeScript code**.

TypeScript does not run directly in the browser. It must be **compiled** into JavaScript. The compiled JavaScript is what actually runs in the browser or in Node.js.

You install TypeScript using `npm`. The TypeScript compiler is called `tsc`.
```powershell
npm install typescript
```

---

## Variables In JavaScript

In JavaScript there are two ways to declare a variable. The first way is using `let` which can be updated, but cannot be redeclared in the same scope. It is most commonly used for variables that change.
```js
let age = 20;
```

The second way is using `const`. Unlike `let`, the variable cannot be reassigned and it must be initialized immediately. It is used for values that **should not change**. Using `const` does not mean the value is immutable, objects and arrays can still change internally.
```js
const name = "angelo";
```

Common JavaScript variable types.
```js
let number = 10;                      // Number
let text = "Hello";                   // String
let isReady = true;                   // Boolean
let nothing = null;                   // Null
let notDefined;                       // Undefined
let list = [1, 2, 3];                 // Array
let user = { name: "Sam", age: 22 };  // Object

```

JavaScript Is Dynamically Typed, this means, you **do not specify types** and the type is determined **at runtime**.

---

## Variables In TypeScript

In TypeScript, when declaring a variable, you have to mention its type, unlike JS. The two ways of declaring a variable still works the same with `let` and `const`, however now there is type safety. To declare a variable you use `varibale : type`.
```ts
let age: number = 20;
```

Common Typescript variable types.
```ts
let age: number = 20;
let name: string = "Alex";
let isStudent: boolean = true;
```

TypeScript Arrays.
```ts
let numbers: number[] = [1, 2, 3];
let names: string[] = ["Alice", "Bob"];
```

TypeScript Objects.
```ts
let user: { name: string; age: number } = {
  name: "Sam",
  age: 22
};
```

The above is called an inline object, it is created once and it is not reusable. To create a reusable object, you can use a `type` alias. This is easier to maintain and more efficient.
```ts
type User = {
  name: string;
  age: number;
};
```

When defining reusable objects you can use `name?: string` to make the specified variable optional.

Then you can use the object like this repetitively. Editing the main `type` will affect the objects everywhere in your project.
```ts
let user: User = {
  name: "Sam",
  age: 22
};
```

You can check the type of a variable using `typeof`. The type of `user` is object in this case.
```ts
typeof user;
```

When declaring a variable, if you set it as a number, but the variable is actually a string, TS will throw a compile time error.

---

## Operators

Booleans and Logical operators.
- And `&&`.
- Or `||`.
- Not `!`.

Numbers and Arithmetic operators.
- Addition `+`.
- Subtraction `-`.
- Multiplication `*`.
- Division `/`.
- Exponential `**`.
- Remainder `%`.

Comparison operators.
- Assigning `=`.
- Comparing values `==`.
- Comparing types `===`.

---
## Conditionals

Shorthand conditionals.
```js
condition ? code if true : code if false;
```

Longhand conditionals.
```js
if (condition) {
	code;
} else if (condition) {
	code;
} else {
	code;
}
```

When using `else if`, if one line is true, the rest of the lines will be skipped. When using `if`, all lines will be checked even if one is true.

---

## Functions

Normal Function.
```js
function name () {
	code;
}
```

Arrow Function.
```js
const name = () => {
	code;
}
```

The function can later be called using `name()`, this works the same as any other programming language. 

In Typescript, you can add type validation to function parameters and outputs.
```ts
const name = (variable : type, varibale : type) : outputType => {
	code;
}
```

---

## Object Destructuring

The following is and example of a TypeScript Object.
```ts
let user: { name: string; age: number } = {
  name: "Sam",
  age: 22
};
```

Destructuring the Object.
```ts
let { name, age } = user;
```

This is equivalent to the following.
```ts
let name = user.name;
let age = user.age;
```

Without destructuring you would need to do `user.name` to access the name variable, but with destructuring, you can access the name variable using `name`.

You can also destructure in function parameters. The following example also shows an example of how string concatenation works in JavaScript.
```ts
function printUser({ name, age }: { name: string; age: number }) {
  console.log(`${name} is ${age} years old`);
}
```

The function can later be called using `printUser(user)`.

---

## Classes In TypeScript

You can build a class in two ways, the first way is to use a constructor with separate parameters.
```ts
class Product {
	private id!: number;
	private name!: string;
	
	constructor(id: number, name: string) {
		this.id = id;
		this.name = name;
	}
}
```

Calling the class.
```ts
const laptop = new Product(1, "Laptop")
```

The second way to build a class is to use a constructor with single object parameters.
```ts
class Product {
	private id!: number;
	private name!: string;
	
	constructor(product {id: number, name: string}) {
		this.id = id;
		this.name = name;
	}
}
```

Calling the class.
```ts
const laptop = new Product({id: 1, name: "Laptop"})
```

However there is a twist, in TypeScript classes, **all properties must be initialized** either **at declaration**, or **inside the constructor**. Otherwise, TypeScript complains by saying: property 'name' has no initializer and is not definitely assigned in the constructor. To solve this problem when defining the variable you can use the `!` in this way `private name!: string`. The `!` tells TypeScript: trust me, this property will be assigned before it’s used. Another way of solving this problem is by using `private name: string = ""`.

---

## Arrays

Arrays are objects that can hold **any type** and even mixed types.
```js
let numbers = [1, 2, 3];
let mixed = [1, "hello", true];
let empty = [];
```

Accessing elements in an array.
```js
numbers[0]; // 1
numbers[2]; // 3
```

Modifying arrays.
```js
numbers[1] = 42;     // [1, 42, 3]
numbers.push(4);     // Add to end [1, 42, 3, 4]
numbers.pop();       // Remove last [1, 42, 3]
numbers.unshift(0);  // Add to start [0, 1, 42, 3]
numbers.shift();     // Remove first [1, 42, 3]
```

Looping over an array using a for loop.
```js
for (let i = 0; i < numbers.length; i++) {
  console.log(numbers[i]);
}
```

Looping over an array using a shorthand for loop.
```js
for (let i in numbers) {
  console.log(i);
}
```

Common array methods.
```js
numbers.length;                        // Size
numbers.includes(42);                  // True
numbers.indexOf(42);                   // 1

numbers.map(n => n * 2);               // [2, 84, 6]
numbers.filter(n => n > 2);            // [42, 3]
numbers.forEach(n => console.log(n));
```

In modern JS, the `map` and `forEach` methods replace for loops, unless you need core for loop functionality like break and continue.

When using TypeScript, arrays work the exact same but just with type specifications.
```ts
let numbers: number[] = [1, 2, 3];
let names: string[] = ["Alice", "Bob"];
let mixed: (number | string)[] = [1, "hi", 2];
```

When pushing a number to an array of strings, TS will throw a compile time error.

---

## Tuples In TypeScript

In TypeScript, there is a special object called a tuple. It has fixed structure, length, and order.
```ts
let user: [string, number] = ["Alice", 20];
```

---

## DOM Manipulation

**DOM is the  Document Object Model**. It’s how the browser represents your HTML **as a tree of objects**. Each HTML element becomes a **node** you can access and change with JS.

Example HTML.
```html
<button id="btn">Click me</button> <p id="text">Hello!</p>
```

Selecting elements.
```js
const btn = document.getElementById("btn");
```

| Method                   | Returns                 | Example                                   |
| ------------------------ | ----------------------- | ----------------------------------------- |
| `querySelector`          | first match             | `document.querySelector("#btn")`          |
| `querySelectorAll`       | NodeList of all matches | `document.querySelectorAll(".item")`      |
| `getElementsByClassName` | HTMLCollection          | `document.getElementsByClassName("item")` |
| `getElementsByTagName`   | HTMLCollection          | `document.getElementsByTagName("li")`     |

Changing content and styles.
```js
const text = document.getElementById("text");
text.innerHTML = "New text";
text.style.color = "red";
```

Creating a new element.
```js
const newDiv = document.createElement("div");
newDiv.textContent = "I’m new!";
document.body.append(newDiv);
```

---

## Event Listeners

**Event listeners** are how JavaScript **reacts to user actions** like clicks, typing, hovering, and submitting forms.
```html
<button id="btn">Click me</button>
```

```js
const btn = document.getElementById("btn");

btn.addEventListener("click", () => {
  console.log("Button clicked!");
});
```

Other event listeners.
- Mouseover
- Mouseout
- Input

---

## The Asynchronous Model

JavaScript is **single-threaded**, meaning it can only execute **one task at a time** on the main thread. However, some tasks like network requests, reading files, timers, or fetching data from an API take times. If JavaScript waited for these to finish **synchronously**, the entire program would **freeze**.

The solution to this problem is Asynchronous Programming. JavaScript uses an **asynchronous, non-blocking model** to handle long-running tasks **without stopping execution**.

There are three ways to handle Asynchronous Code. The first way is callbacks, and this is the oldest way to do it. This is called polling and it is used when you want something to be delayed in order for it to wait for something else to finish. This is the most inefficient way to do it.
```js
setTimeout(() => {
  console.log("Done");
}, 1000);
```

The second way is Promises. A **Promise** represents a value that will be available **in the future**. It can be either, pending, fulfilled, or rejected.
```js
fetch(url)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

The third way is using Async and Await. This is the most modern way to handle Asynchronous Code. When using Async and Await, `await` pauses **only the function**, not the entire program which helps us run many tasks at once.
```js
async function fetchData() {
  const response = await fetch("https://api.example.com/data");
  const data = await response.json();
  console.log(data);
  return data;
}
```

Or using an arrow function.
```js
const fetchData = async () => {
  const response = await fetch("https://api.example.com/data");
  const data = await response.json();
  console.log(data);
  return data;
}
```

As you can see in the examples above, we are using Asynchronous JavaScript in order to fetch data form an API, this bring us to fetching.

---

## Fetching From An API

In JavaScript `fetch` is a **Web API** used to make HTTP requests like GET, POST, PUT, and DELETE. It is **asynchronous** and it returns a promise so you can use it with `then` and `catch` like we saw in the example above.

The best modern way to fetch data, is using Async and Await paired with error handling using `try` and `catch`.
```js
async function fetchData() {
  try {
    const response = await fetch(url);
    const data = await response.json();
    console.log(data);
    return data;
  } catch (error) {
    console.error(error);
  }
}
```

To fully understand `fetch` you also need to know if has options. you can mention the `method` and `headers`. If you do not mention the method, `fetch` uses GET by default.
```js
async function createUser() {
  const response = await fetch("/users", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    }
  });

  const data = await response.json();
  console.log(data);
}
```

HTTP response status codes.
- `2xx` Success.
- `3xx` Redirect.
- `4xx` Client error.
- `5xx` Server error.

---

## Enums In TypeScript

An **enum or enumeration** is a way to define a **fixed set of named values**.
```ts
enum Status {
  Loading,
  Success,
  Error
}
```

Using an enum.
```ts
let current: Status = Status.Loading;

if (current === Status.Success) {
  console.log("Done");
}
```

---

## Exporting Defaults

When you want to use a method from a file in a different file, you need to export that method and import it in the other file.

Exporting methods. You normally add this line at the end of a file.
```js
export default {method, method}
```

Importing methods.
```js
import method from 'path'
```

---