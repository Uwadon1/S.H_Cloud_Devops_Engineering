# Basic JavaScript Syntax Documentation

## Introduction

JavaScript is a versatile and widely-used programming language primarily employed for adding interactivity to web pages. It's a client-side scripting language, meaning it runs in the user's browser rather than on a server. Understanding the basic syntax of JavaScript is fundamental for writing efficient and functional code.

__1.__ __Comments:__

Comments are used to annotate code for clarity and documentation. JavaScript supports two types of comments:

__Single-line comments:__ __// This is a single-line comment__

__Multi-line comments:__

```bash
/*
This is a multi-line
comment
*/
```

__2.__ __Variables:__

Variables are containers for storing data values. In JavaScript, you declare variables using the ```var, let, or const``` keywords.

- __var__: Declares a variable globally or locally to an entire function regardless of block scope.
- __let:__ Declares a block-scoped variable that can be reassigned.
- __const:__ Declares a block-scoped variable that cannot be reassigned after initialization.

Example:
```bash
var a = 5;
let b = 'Hello';
const c = true;
```

__3.__ __Data Types:__
JavaScript supports various data types:

__Primitive Data Types:__ ```Number, String, Boolean Undefined, Null```

__Composite Data Types:__ ```Object, Array```

__Special Data Types:__ ```Function```

Example:

```bash
let num = 10;
let str = "JavaScript";
let bool = true;
let arr = [1, 2, 3];
let obj = { name: "John", age: 30 };
let func = function() {
    console.log("Hello!");
};
```

__4.__ __Operators:__

JavaScript includes various operators for performing operations on variables and values.

__Arithmetic Operators:__ ```+, -, *, /, %```

__Assignment Operators:__ ```=, +=, -=, *=, /=```

__Comparison Operators:__ ```==, ===, !=, !==, >, <, >=, <=```
__Logical Operators:__``` &&, ||, !```

__String Operators:__ ```+ (concatenation)```

__Conditional (Ternary)__ ```Operator: condition ? value1 : value2```

Example:

```bash
let x = 10;
let y = 5;
let z = x + y; // Addition
let result = (x > y) ? "x is greater than y" : "x is less than or equal to y"; // Ternary Operator
```

__5.__ __Control Structures:__

JavaScript provides control structures to control the flow of the program.

- __Conditional Statements:__ ```if, else if, else```

- __Switch Statement:__ Executes a block of code based on different cases

- __Loops:__ ```for, while, do...while```

Example:

```bash
let num = 10;
if (num > 0) {
    console.log("Positive number");
} else if (num < 0) {
    console.log("Negative number");
} else {
    console.log("Zero");
}

let i = 0;
while (i < 5) {
    console.log(i);
    i++;
}
```

__6.__ __Functions:__

Functions are blocks of reusable code. JavaScript functions can be defined using the function keyword.

- __Function Declaration:__ Declared function can be called before they are defined.
- __Function Expression:__ Assigning a function to a variable.

Example:

```bash
// Function Declaration
function greet(name) {
    return "Hello, " + name + "!";
}

// Function Expression
let greet = function(name) {
    return "Hello, " + name + "!";
};

console.log(greet("John"));
```

### Conclusion:

This documentation covers the basic syntax of JavaScript, providing a foundation for writing JavaScript code. Understanding these concepts is essential for developing dynamic and interactive web applications. Further learning and practice will deepen your understanding of JavaScript programming.









