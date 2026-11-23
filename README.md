# The Function Returns — Returning Values From Functions

[![Tests](https://github.com/RickRieger/the-function-returns/actions/workflows/tests.yml/badge.svg)](https://github.com/RickRieger/the-function-returns/actions/workflows/tests.yml)

A JavaScript exercise from the **Code Immersives** web development bootcamp in New York City (February 2021). It's the third in a series, after [Functions Ahoy](https://github.com/RickRieger/functions-ahoy) and [Functions With Parameters](https://github.com/RickRieger/functions-with-parameters). The first half reviews the old way, functions that change global variables. The second half introduces **`return`**: functions that hand back a value instead of changing anything. [Jest](https://jestjs.io/) tests check each function. This repo is my fork of the assignment. My solution is at the top of [`main.test.js`](main.test.js), where this assignment puts the code.

**Skills practiced:** `return` values vs. side effects, multiple parameters, division and remainder (`/`, `%`), and building strings.

**Built with:** JavaScript (Node.js) and Jest 24. There's no web page; the badge above shows whether the tests currently pass on GitHub.

---

## Contents

- [My solution](#my-solution)
- [Changing a variable vs. returning a value](#changing-a-variable-vs-returning-a-value)
- [Run the tests](#run-the-tests)
- [How it's verified](#how-its-verified)
- [Cleanup log (November 2026)](#cleanup-log-november-2026)
- [Original assignment (from the instructor)](#original-assignment-from-the-instructor)

---

## My solution

Written on **2021-02-17** (commits "homework", "update" and "finished"). All 7 of the assignment's tests passed. *(Shown as it is after the 2026 cleanup; see the log below for what changed.)*

```js
let greeting = 'Hello';
let sum = 0;
let prod = 0;

// Functions that change global variables
function greet(person) {
  greeting = 'Hello ' + person;      // greet('mess') → greeting is 'Hello mess'
}

function sumOfTwo(a, b) {
  sum = a + b;                       // sumOfTwo(2, 4) → sum is 6
}

function multiply(a, b, c) {
  prod = a * b * c;                  // multiply(2, 2, 2) → prod is 8
}

// Functions that return a value
function sayHi(name) {
  return 'Hello ' + name + '!';      // sayHi('Colin') → 'Hello Colin!'
}

function returnWhatISay(sentence) {
  return sentence;                   // returnWhatISay('Colin') → 'Colin'
}

function divide(a, b) {
  return a / b;                      // divide(10, 5) → 2
}

function remainder(a, b) {
  return a % b;                      // remainder(28, 6) → 4
}
```

## Changing a variable vs. returning a value

This is the main lesson of the assignment.

| | Changes a global variable | Returns a value |
|---|---|---|
| Example | `sumOfTwo(2, 4)` sets `sum` to `6` | `divide(10, 5)` gives back `2` |
| How you use it | Call it, then read the variable: `sumOfTwo(2, 4); console.log(sum);` | Use the call itself as a value: `const answer = divide(10, 5);` |
| Can it be reused for different results? | Each call overwrites the same variable | Yes: `divide(10, 5) + divide(15, 3)` is `7` |
| How the test checks it | Calls the function, then checks the variable | Checks what the function call evaluates to |

Returning values is usually the better design. The function doesn't depend on or change anything outside itself, so it's easier to test and reuse. The rest of the course builds on it.

A few other details:
- `returnWhatISay` returns its input unchanged. It looks trivial, but it shows that a parameter's value can be passed straight back out.
- `%` (*modulus*) gives the remainder: 28 ÷ 6 = 4 remainder **4**.
- The `sayHi` test also passes `'Mesi!'` and expects `'Hello Mesi!!'`: the function adds its own `!` no matter what's in the name.

## Run the tests

You need [Node.js](https://nodejs.org/).

```bash
git clone https://github.com/RickRieger/the-function-returns.git
cd the-function-returns
npm ci
npm run test:ci   # runs all tests once and exits
npm test          # watch mode, as in the original assignment: re-runs on every save
```

Expected result: `Tests: 8 passed, 8 total` (the 7 original tests plus 1 added in the cleanup below).

## How it's verified

[`.github/workflows/tests.yml`](.github/workflows/tests.yml) runs `npm run test:ci` on GitHub Actions after every push, and the badge at the top shows the result. This repo has no web page, so instead of a live site, a passing test run is how it shows it works.

---

## Cleanup log (November 2026)

All changes below were made on **2026-11-23** on the branch `cleanup-and-ci`, checked by running the tests, and merged into `master`. Each is its own commit. Nothing below the assignment's "DON'T CHANGE ANYTHING BELOW HERE" line was edited.

### 1. `greeting` now starts as `'Hello'`, and a test checks the starting values

**Why:** the instructions say *"Create a variable named `greeting` and give it the value 'Hello'"*, but it was declared as `'hello'` (lowercase). No original test checks starting values, only what happens after the functions run, so this passed unnoticed.

```js
// Before
let greeting = 'hello';

// After
let greeting = 'Hello';
```

I added a `starting values` test in the code section. Because it's registered before the original tests, it runs before any function changes the variables. It checks that `greeting` starts as `'Hello'` and `sum` and `prod` start at `0`. I confirmed it **fails on the old code** and passes on the fix.

*A related quirk in the original assignment:* the instructions say `greet` should set `greeting` to `'Hello'` plus a space plus the name, but the test's description says "its old value plus the given string". My solution follows the instructions and hard-codes `'Hello '`, which satisfies both now that the starting value is `'Hello'`.

### 2. Tidied the code section

**Why:** indentation was inconsistent (three function bodies weren't indented), some semicolons were missing, spacing was uneven (`a *b*c`), and there were about 20 blank lines before the instructor's section. I also grouped the functions under two comments that match the assignment's two halves. No behavior changed.

```js
// Before
function sumOfTwo(a,b){
sum = a + b;
}

function multiply(a,b,c){
  prod = a *b*c
}

function returnWhatISay(sentence){
return sentence
}

// After
// Functions that change global variables
function sumOfTwo(a, b) {
  sum = a + b;
}

function multiply(a, b, c) {
  prod = a * b * c;
}

// Functions that return a value
function returnWhatISay(sentence) {
  return sentence;
}
```

### 3. Automatic test runs on GitHub, and a `.gitignore` fix

**Why:** `npm test` runs Jest in *watch mode*, which never exits, so an automated check couldn't use it. I added a run-once command and a GitHub Actions workflow. `.gitignore` also listed `node_modules` twice.

```jsonc
// Before (package.json)
"scripts": { "test": "jest --watch-all" }

// After
"scripts": {
  "test": "jest --watch-all",   // unchanged: watch mode while working
  "test:ci": "jest --ci"        // new: run once and exit
}
```

### 4. Rewrote this README section

Added everything above. The instructor's original instructions are below, unchanged.

---

## Original assignment (from the instructor)

*The text below is the assignment README as written by the Code Immersives instructor, unchanged.*

### The Function Returns

### Setup

1. Navigate to this directory in your terminal.
2. Run `code .` to open the project in VS Code.
3. Open `main.test.js` (you can use Command-P to start typing in the name!).
3. In a terminal (preferably a full-screen one), run `jest --watch-all` to start testing.


### Workflow

You'll be working in **`main.test.js`**, creating the variables needed with the values asked for, according to the specifications given in the tests. Check your terminal for feedback on which aspect of the problem you have yet to complete, and read the specifications' actual code implementation for extra help; it makes explicit exactly what outputs are expected given the test inputs.


### Guidelines:

* Please do **not** call the functions; just declare them! You can call them to check for your own testing purposes, but then either delete or comment out the line.


### Tasks

##### Global Variables, just to refresh us on the old way of doing things.

* Create a variable named `greeting` and give it the value 'Hello'.
* Create a variable named `sum` and give it the value 0.
* Create a variable named `prod` and give it the value 0.
* Create a function named  `greet` that will take one parameter (of type string). The function will change the value of `greeting` to 'Hello' followed by a space followed by the value of the parameter.
* Create a function named  `sumOfTwo` that will take two parameters (of type number). The function will change the value of `sum` to be equal to the sum of the two parameters
* Create a function named  `multiply` that will take three parameters (of type number). The function will change the value of `prod` to be equal to the product of the three parameters.


##### Let's RETURN stuff.

* Create a function named  `sayHi` that will take one parameter and return a personalized greeting

   INPUT:  sayHi("Charlotte"); 

   OUTPUT:  "Hello Charlotte!";

   INPUT:  sayHi("Colin"); 

   OUTPUT:  "Hello Colin!";

* Create a function named  `returnWhatISay` that will take a string sentence and returns that sentence

   INPUT:  returnWhatISay("Hello students, how are you"); 

   OUTPUT:  "Hello students, how are you";

* Create a function named  `divide` that will take two parameters and return the result

   INPUT:  divide(10,5); 

   OUTPUT:  2;

* Create a function named `remainder` that takes two parameters and return the remainder that we get when those two numbers get divided

   INPUT:  remainder(10,3); 

   OUTPUT:  1;
