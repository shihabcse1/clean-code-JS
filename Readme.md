# Clean Code Javascript

## Table of Contents

1. [Introduction](#introduction)
2. [Variables](#variables)
3. [Functions](#functions)
4. [Objects and Data Structures](#objects-and-data-structures)
5. [Classes](#classes)
6. [SOLID](#solid)
7. [Testing](#testing)
8. [Concurrency](#concurrency)
9. [Error Handling](#error-handling)
10. [Formatting](#formatting)
11. [Comments](#comments)
12. [DRY](#dry)
13. [KISS](#kiss)
14. [YAGNI](#yagni)

## Introduction

Clean code হচ্ছে code, যেটা readable, reusable, refactorable

## **Variables**

### 1.1 Use meaningful and pronounceable variable names:

**❌**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**✅**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ back to top](#table-of-contents)**

### 1.2 Use the same vocabulary for the same type of variable:

আমরা জানি সবই আসলে info বা data, inside user.

**❌**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**✅**

```javascript
getUser();
```

**[⬆ back to top](#table-of-contents)**

### 1.3 Use readable and searchable names

Truth is - আমরা code লিখার চেয়ে code read করি বেশি। Tools like
[buddy.js](https://github.com/danielstjules/buddy.js) and
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
can help identify unnamed constants.

**❌**

```javascript
// What the heck is 525600 for?
for (let i = 0; i < 525600; i++) {
  runCornJob();
}
```

**✅**

```javascript
// Declare them as capitalized named constants.
const MINUTES_IN_A_YEAR = 525600;
for (let i = 0; i < MINUTES_IN_A_YEAR; i++) {
  runCornJob();
}
```

**[⬆ back to top](#table-of-contents)**

### 1.4 Use explanatory variables

**❌**

```javascript
const cityStateRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  cityStateRegex.match(cityStateRegex)[1],
  cityStateRegex.match(cityStateRegex)[2]
);
```

**✅**

```javascript
const cityStateRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const match = cityStateRegex.match(cityStateRegex);
const city = match[1];
const state = match[2];
saveCityZipCode(city, state);
```

**[⬆ back to top](#table-of-contents)**

### 1.5 Avoid Mental Mapping

Explicit is better than implicit.

**❌**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**✅**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ back to top](#table-of-contents)**

### 1.6 Don't add unneeded context

If your class/object name tells you something, don't repeat that in your
variable name.

**❌**

```javascript
const user = {
  userId: "12",
  userName: "Alex",
};
```

**✅**

```javascript
const user = {
  id: "12",
  name: "Alex",
};
```

**[⬆ back to top](#table-of-contents)**

### 1.7 Use default parameters instead of short circuiting or conditionals:

Default parameters গুলো short circuiting এর চেয়ে cleaner. তাই undefined arguments এর জন্য default values provide এর ক্ষেত্রে সতর্ক থাকতে হবে। অন্যান্য "falsy" values values যেমনঃ `''`, `""`, `false`, `null`, `0`, and `NaN`, will not be replaced by a default value.

**❌**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**✅**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

## **Functions**

### 2.1 Function arguments (2 or fewer ideally):

Function এ parameterএ limit(highest 2 argument) থাকা important, এতে testing easy হয় কারণ tons of different case of seperate argument নিয়ে চিন্তা করতে হয় না।

এটির কয়েকটি সুবিধা রয়েছে:

1. Function এ argument পাঠানো হয় এবং তা destructuring করা হয়, এটা আসলে object এর primitive value গুলোকে clone করে. Note: Destroyed objects and arrays are not cloned from the argument object.
2. এছাড়া destruction না করা হলে, unused properties এর জন্য Linter warn দিতে পারে না

**❌**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);
```

**✅**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true,
});
```

**[⬆ back to top](#table-of-contents)**

### 2.2 Functions should do one thing:

যার ফলে easily refactor করা যায়, code more clean হয় এবং এটা easier to test.

**❌**

```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**✅**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ back to top](#table-of-contents)**

### 2.3 Function names should say what they do:

**❌**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
addToDate(date, 1);
```

**✅**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ back to top](#table-of-contents)**

### 2.4 Functions should only be one level of abstraction:

Function এ একাধিক abstraction level থাকলে, এটা সাধারণত অনেক করা বুঝায়। কিন্তু মনে রাখতে হবে যে, Function আকারে split করে কাজ করলে reusability এবং testing করা easier হয় ।

**❌**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**✅**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach((node) => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach((token) => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ back to top](#table-of-contents)**

### 2.5 Remove duplicate code

Duplicate code is bad কারণ আমাদের multiple place এ update করতে হয়

**❌**

```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink,
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio,
    };

    render(data);
  });
}
```

**✅**

```javascript
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience,
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ back to top](#table-of-contents)**

### 2.6 Set default objects with Object.assign

**❌**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true,
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**✅**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true,
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true,
    },
    config
  );
  return finalConfig;
  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ back to top](#table-of-contents)**

### 2.7 Don't use flags as function parameters:

`flag` user কে বলে যে, function টা একের অধিক কাজ করে, এজন্য এটা parameter হিসেবে থাকা ঠিক না। এক্ষেত্রে code কে split করা ভালো।

**❌**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**✅**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ back to top](#table-of-contents)**

### 2.8 Avoid Side Effects (part 1):

একটা function একটা value বা values input হিসেবে নেয় এবং ওপর কোনো value বা values return করে। যদি এর থেকে অন্য কিছু করে, তার মানে side effect produce করতেছে। যেমনঃ writing to a file, modifying some global variable, যদি আসলেই file update করতে হয়, তাহলে only one function use করে করতে হবে।

**❌**

```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**✅**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ back to top](#table-of-contents)**

### 2.9 Avoid Side Effects (part 2)

In JavaScript, some values are unchangeable (immutable) and some are changeable
(mutable). Objects and arrays are two kinds of mutable values so it's important
to handle them carefully when they're passed as parameters to a function. A
JavaScript function can change an object's properties or alter the contents of
an array which could easily cause bugs elsewhere.

Suppose there's a function that accepts an array parameter representing a
shopping cart. If the function makes a change in that shopping cart array -
by adding an item to purchase, for example - then any other function that
uses that same `cart` array will be affected by this addition. That may be
great, however it could also be bad. Let's imagine a bad situation:

The user clicks the "Purchase" button which calls a `purchase` function that
spawns a network request and sends the `cart` array to the server. Because
of a bad network connection, the `purchase` function has to keep retrying the
request. Now, what if in the meantime the user accidentally clicks an "Add to Cart"
button on an item they don't actually want before the network request begins?
If that happens and the network request begins, then that purchase function
will send the accidentally added item because the `cart` array was modified.

A great solution would be for the `addItemToCart` function to always clone the
`cart`, edit it, and return the clone. This would ensure that functions that are still
using the old shopping cart wouldn't be affected by the changes.

Two caveats to mention to this approach:

1. There might be cases where you actually want to modify the input object,
   but when you adopt this programming practice you will find that those cases
   are pretty rare. Most things can be refactored to have no side effects!

2. Cloning big objects can be very expensive in terms of performance. Luckily,
   this isn't a big issue in practice because there are
   [great libraries](https://facebook.github.io/immutable-js/) that allow
   this kind of programming approach to be fast and not as memory intensive as
   it would be for you to manually clone objects and arrays.

**❌**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**✅**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ back to top](#table-of-contents)**

### 2.10 Don't write to global functions:

Polluting globals হচ্ছে bad practice কারণ এটা অন্য library এর সাথে clash করতে পারে। Example: `Array.prototype` এই কারণেই শুধু ES2015/ES6 ক্লাস ব্যবহার করে, `Array` global extend করা ভালো।

**❌**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter((elem) => !hash.has(elem));
};
```

**✅**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter((elem) => !hash.has(elem));
  }
}
```

**[⬆ back to top](#table-of-contents)**

### 2.11 Favor functional programming over imperative programming

JavaScript এর functional flavor আছে, এজন্য এগুলো use করা ভালো এবং easily test করা যায়।

**❌**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500,
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500,
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150,
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000,
  },
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**✅**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500,
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500,
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150,
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000,
  },
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ back to top](#table-of-contents)**

### 2.12 Encapsulate conditionals

**❌**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**✅**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### 2.13 Avoid negative conditionals

**❌**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**✅**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### 2.14 Avoid conditionals

This seems like an impossible task. Upon first hearing this, most people say,
"how am I supposed to do anything without an `if` statement?" The answer is that
you can use polymorphism to achieve the same task in many cases. The second
question is usually, "well that's great but why would I want to do that?" The
answer is a previous clean code concept we learned: a function should only do
one thing. When you have classes and functions that have `if` statements, you
are telling your user that your function does more than one thing. Remember,
just do one thing.

**❌**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**✅**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ back to top](#table-of-contents)**

### 2.15 Avoid type-checking (part 1)

JavaScript is untyped, which means your functions can take any type of argument.
Sometimes you are bitten by this freedom and it becomes tempting to do
type-checking in your functions. There are many ways to avoid having to do this.
The first thing to consider is consistent APIs.

**❌**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**✅**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ back to top](#table-of-contents)**

### 2.16 Avoid type-checking (part 2)

If you are working with basic primitive values like strings and integers,
and you can't use polymorphism but you still feel the need to type-check,
you should consider using TypeScript. It is an excellent alternative to normal
JavaScript, as it provides you with static typing on top of standard JavaScript
syntax. The problem with manually type-checking normal JavaScript is that
doing it well requires so much extra verbiage that the faux "type-safety" you get
doesn't make up for the lost readability. Keep your JavaScript clean, write
good tests, and have good code reviews. Otherwise, do all of that but with
TypeScript (which, like I said, is a great alternative!).

**❌**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**✅**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ back to top](#table-of-contents)**

### 2.16 Don't over-optimize

Modern browsers do a lot of optimization under-the-hood at runtime. A lot of
times, if you are optimizing then you are just wasting your time. [There are good
resources](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
for seeing where optimization is lacking. Target those in the meantime, until
they are fixed if they can be.

**❌**

```javascript
// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**✅**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### 2.17 Remove dead code

Dead code is just as bad as duplicate code. There's no reason to keep it in
your codebase. If it's not being called, get rid of it! It will still be safe
in your version history if you still need it.

**❌**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**✅**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ back to top](#table-of-contents)**

## **Objects and Data Structures**

### 3.1 Use getters and setters

একটা `property` or an `object` looking up এর চেয়ে `getters` and `setters` use করা ভালো । কারণগুলো হলো:

- যখন আমি an object's property getting এর থেকেও আরও কিছু করতে চাই, তখন আমাকে codebase এর সকল accessor find and change করতে হবে না।
- Makes adding validation simple when doing a `set`.
- Internal representation কে Encapsulates করতে পারবো। .
- getter and setter use করলে logging and error handling করা easy
- যখন ইহা server থেকে পাবো, তখন object's properties lazy load করতে পারবো।

**❌**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0,
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**✅**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance,
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ back to top](#table-of-contents)**

### 3.2 Make objects have private members

এটা closures এর মাধ্যমে অর্জন হতে পারে (for ES5 and below).

**❌**

```javascript
const Employee = function (name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**✅**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    },
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ back to top](#table-of-contents)**

## **Classes**

### 4.1 Prefer ES2015/ES6 classes over ES5 plain functions

`class` use করা লাগলে ES6 এর `function` এর পরিবর্তে `class` use করা উচিত। Especially যখন `inheritance` লাগবে তখন।

**❌**

```javascript
const Animal = function (age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function (age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function (age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**✅**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ back to top](#table-of-contents)**

### 4.2 Use method chaining

এটা code কে expressive, and less verbose করে। অনেক library(jQuery and Lodash) তেই আমরা এটা দেখি।
class functions এর মধ্যে, simply every function এর শেষে return `this`, তাহলেই class method chain করা যাবে। Note: এটা আসলে easy concept। function টা `this` return না করলে `undefine` return করে।

**❌**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**✅**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ back to top](#table-of-contents)**

### 4.3 Prefer composition over inheritance

As stated famously in [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
   relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
   (Change the caloric expenditure of all animals when they move).

**❌**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**✅**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

## **SOLID**

### 6.1 Single Responsibility Principle (SRP)

এটা বলে যে - "একটা `class` change করার জন্য একটাই reason থাকবে". একটা class change করতে অনেক reason থাকলে, এটা change করতে অনেক সময়ও লাগবে। একটা class এ অনেক বেশি functionalities থাকলে, এটা আরও কোন কোন dependent module এ affect করবে তা বোঝাও কষ্টকর। এটা developer কে যা build করতেছে তার context এবং responsibility বুঝতে এবং কখন তা change করা প্রয়োজন তা বুঝতে সাহায্য করে।

**❌**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**✅**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ back to top](#table-of-contents)**

### 6.2 Open/Closed Principle (OCP)

এটা বলে যে - "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." এই principle বলে যে - existing code modify না করেই new functionalites add করতে হবে।

**❌**

```javascript
let icecreamFlavor = ["chocolate", "vanilla"];
let icecreamMaker = {
  makeIceCream(flavor) {
    if (iceCreamFlavors.indexof(flavor) > -1) {
      console.log("You have an icecream");
    } else {
      console.log("No Icecream for you");
    }
  },
};
export default icecreamMaker;
```

**✅**

```javascript
let icecreamFlavor = ["chocolate", "vanilla"];
let icecreamMaker = {
  makeIceCream(flavor) {
    if (iceCreamFlavors.indexof(flavor) > -1) {
      console.log("You have an icecream");
    } else {
      console.log("No Icecream for you");
    }
  },
  addFlavor(flavor) {
    iceCreamFlavors.push(flavor);
  },
};
export default icecreamMaker;
```

**[⬆ back to top](#table-of-contents)**

### 6.3 Liskov Substitution Principle (LSP)

Substitution মানে 'প্রতিস্থাপন'। এই principle বলে - "child class এর কখনো parent class এর 'type definition' and 'property' break করা উচিত নয়"
এটা আসলে inheritance এর ক্ষেত্রে use হচ্ছে।

**❌**

```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  area() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  constructor(sideLength) {
    super(sideLength, sideLength);
  }
}

function calculateArea(rectangle) {
  return rectangle.area();
}

const rectangle = new Rectangle(4, 5);
console.log(calculateArea(rectangle)); // Output: 20

const square = new Square(4);
console.log(calculateArea(square)); // Output: 16 (Incorrect! Should be 16, but returns 20)
```

**✅**

```javascript
class Shape {
  area() {
    throw new Error("This method should be overridden");
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  area() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(sideLength) {
    super();
    this.sideLength = sideLength;
  }

  area() {
    return this.sideLength ** 2;
  }
}

function calculateArea(shape) {
  return shape.area();
}

const rectangle = new Rectangle(4, 5);
console.log(calculateArea(rectangle)); // Output: 20

const square = new Square(4);
console.log(calculateArea(square)); // Output: 16
```

**[⬆ back to top](#table-of-contents)**

### 6.4 Interface Segregation Principle (ISP)

Segregation মানে 'পৃথকীকরণ'। এই principle এর মূল কথা হলো - "interface divide করে করে ছোট করে ফেলতে হবে। মানে interface এর কোনো method কে implement করতে আমাদের যাতে বাধ্য করা না হয়"
আমরা জানি, interface এর মধ্যে যে method গুলো থাকে সেগুলোর body থাকে না, এবং ওই interface গুলোকে যারা implement করে, তাদেরকে ওই implementation গুলোর definition দিতে হয়।

**❌**

```javascript
class Printer {
  constructor() {}

  print() {
    throw new Error("Not implemented");
  }

  scan() {
    throw new Error("Not implemented");
  }
}

class SimplePrinter extends Printer {
  constructor() {
    super();
  }

  print() {
    console.log("Printing...");
  }
}

class AdvancedPrinter extends Printer {
  constructor() {
    super();
  }

  print() {
    console.log("Printing...");
  }

  scan() {
    console.log("Scanning...");
  }
}

const simplePrinter = new SimplePrinter();
simplePrinter.print(); // Output: Printing...
// simplePrinter.scan(); // Error: Not implemented

const advancedPrinter = new AdvancedPrinter();
advancedPrinter.print(); // Output: Printing...
advancedPrinter.scan(); // Output: Scanning...
```

**✅**

```javascript
class Print {
  constructor() {}

  print() {
    throw new Error('Not implemented');
  }
}

class Scan {
  constructor() {}

  scan() {
    throw new Error('Not implemented');
  }
}

class SimplePrinter extends Print {
  constructor() {
    super();
  }

  print() {
    console.log('Printing...');
  }
}

class AdvancedPrinter extends Print, Scan {
  constructor() {
    super();
  }

  print() {
    console.log('Printing...');
  }

  scan() {
    console.log('Scanning...');
  }
}

const simplePrinter = new SimplePrinter();
simplePrinter.print(); // Output: Printing...
// simplePrinter.scan(); // Error: scan is not a function

const advancedPrinter = new AdvancedPrinter();
advancedPrinter.print(); // Output: Printing...
advancedPrinter.scan(); // Output: Scanning...

```

**❌**

```typescript
interface Worker {
  work(): void;
  takeBreak(): void;
}

class Developer implements Worker {
  work() {
    console.log("Developer is working...");
  }

  takeBreak() {
    console.log("Developer is taking a break...");
  }
}

class Robot implements Worker {
  work() {
    console.log("Robot is working...");
  }

  takeBreak() {
    console.log("Robot does not take breaks...");
  }
}

const developer = new Developer();
developer.work(); // Output: Developer is working...
developer.takeBreak(); // Output: Developer is taking a break...

const robot = new Robot();
robot.work(); // Output: Robot is working...
robot.takeBreak(); // Output: Robot does not take breaks...
```

**✅**

```typescript
interface Workable {
  work(): void;
}

interface Breakable {
  takeBreak(): void;
}

class Developer implements Workable, Breakable {
  work() {
    console.log("Developer is working...");
  }

  takeBreak() {
    console.log("Developer is taking a break...");
  }
}

class Robot implements Workable {
  work() {
    console.log("Robot is working...");
  }
}

const developer = new Developer();
developer.work(); // Output: Developer is working...
developer.takeBreak(); // Output: Developer is taking a break...

const robot = new Robot();
robot.work(); // Output: Robot is working...
// robot.takeBreak(); // Error: Property 'takeBreak' does not exist on type 'Robot'.
```

**[⬆ back to top](#table-of-contents)**

### 6.5 Dependency Inversion Principle (DIP)

This principle states two essential things:

1. High-level modules should not depend on low-level modules. Both should
   depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
   abstractions.

একটা class ওপর একটা class এর উপর পুরোপুরি depended হতে পারবে না। মানে এই principle বলে, আমাদের `tightly coupled code` লিখা যাবে না।

**❌**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // ❌ We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**✅**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ back to top](#table-of-contents)**

## **Testing**

এটা mean করে program এ যদি কোনো ভুল থাকে তাহলে টা runtime এ successfully identified হয়েছে। In addition to having a great testing framework, you also need to use a
[good coverage tool](https://gotwarlost.github.io/istanbul/).

test না লিখার কোনো excuse নাই। কিছু JS test framwork হলো: [plenty of good JS test frameworks](https://jstherightway.org/#testing-tools)। আমাদের উচিত প্রত্যেক new feature/module এর জন্য test লেখা।
আপনি যদি Test Driven Development (TDD) prefer করেন, তাহলে ভালো, কিন্তু main point হচ্ছে you are reaching your coverage goals before launching any feature, or refactoring an existing one.

### 7.1 Single concept per test

**❌**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**✅**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ back to top](#table-of-contents)**

## **Concurrency**

### 8.1 Use Promises, not callbacks

Callbacks আসলে clean না, এটা callback hell create করতে পারে। ES2015/ES6 Promises হচ্ছে build-in global type.

**❌**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, (writeErr) => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**✅**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then((body) => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch((err) => {
    console.error(err);
  });
```

**[⬆ back to top](#table-of-contents)**

### 8.2 Async/Await are even cleaner than Promises

Promises এর চেয়েও বেশি cleaner solution হচ্ছে ES2017/ES8 এর `async` and `await`.

**❌**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then((body) => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch((err) => {
    console.error(err);
  });
```

**✅**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin");
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle();
```

**[⬆ back to top](#table-of-contents)**

## **Error Handling**

Thrown errors are a good thing! কারণ program এ কোনো কিছু wrong হইলে runtime এ successfully identify করা যায়। এটা আমাদের বলে যে, current stack এ function টার execution বন্ধ আছে, killing the process (in Node) এবং console এর মাধ্যমে stack trace notify করে।

### 9.1 Don't ignore caught errors

Logging the error চেয়ে `console.log` অনেক ভালো। যদি any bit of code কে দিয়ে wrap করা হয়, তার মানে - "আপনি মনে করেন একটা error ঘটতে পারে, সুতরাং আপনার একটা plan থাকা উচিত বা আপনার একটা code path create করা উচিত।"  

**❌**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**✅**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### 9.2 Don't ignore rejected promises

এই একই reason এ, `try/catch` error caught করলে ignore করা উচিত না।

**❌**

```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    console.log(error);
  });
```

**✅**

```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ back to top](#table-of-contents)**

## **Formatting**

Formatting is subjective. মূল কথা হচ্ছে, team এর মধ্যে code টা consistent রাখা।
Formatting automate করার জন্য [tons of tools](https://standardjs.com/rules.html) আছে।

### 10.1 Use consistent capitalization:

যেহেতু JavaScript untyped, তাই capitalization অনেক কিছু বলে `variable` ও `function` সম্পর্কে।

**❌**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**✅**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ back to top](#table-of-contents)**

### 10.2 Function callers and callees should be close:

যদি একটা function ওপর একটা function কে call করে, তাহলে source file এ তাদের vertically close রাখতে হয়। Ideally, `caller(যে call করতেছে)` কে `callee(যাকে call করতেছে)` এর উপরে রাখতে হয়। আমরা code কে top-to-bottom পড়ি, newspaper এর মতো।

**❌**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**✅**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ back to top](#table-of-contents)**

## **Comments**

### 10.3 Only comment things that have business logic complexity.

Comments কোনো requirement না, এটা একটা apology. Good code _mostly_ documents itself.

**❌**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**✅**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ back to top](#table-of-contents)**

### 10.4 Don't leave commented out code in your codebase

Version control এই কাজই করতেছে।

**❌**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**✅**

```javascript
doStuff();
```

**[⬆ back to top](#table-of-contents)**

### 10.4 Don't have journal comments

Version control এই কাজই করতেছে। Use `git log` to get history!

**❌**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**✅**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ back to top](#table-of-contents)**

### 10.5 Avoid positional markers

They usually just add noise.

**❌**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar",
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function () {
  // ...
};
```

**✅**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar",
};

const actions = function () {
  // ...
};
```

**[⬆ back to top](#table-of-contents)**

## **DRY**

DRY - "Don't Repeat Youself". এই principle মূলত repetition reduce করে। এটা achieve করার কিছু way হলো:

- Common functionalities এর জন্য separate module করা
- Repetitive value store এর জন্য variable use করা
- Code copying and pasting avoid করা

**❌**

```javascript
// Not DRY
function addNumbers(a, b) {
  console.log(a + b);
}

function subtractNumbers(a, b) {
  console.log(a - b);
}
```

**✅**

```javascript
// DRY
function calculateNumbers(a, b, operator) {
  if (operator === "+") {
    console.log(a + b);
  } else if (operator === "-") {
    console.log(a - b);
  }
}
```

**✅**

```javascript
// More DRY
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

function calculateNumbers(a, b, operator) {
  let result;
  if (operator === "+") {
    result = add(a, b);
  } else if (operator === "-") {
    result = subtract(a, b);
  } else {
    throw new Error("Unsupported operator");
  }
  console.log(result);
}
```

**[⬆ back to top](#table-of-contents)**

## **KISS**

KISS - "Keep It Simple, Stupid". এই principle code simple এবং understandable রাখার ব্যাপারে emphasize করে। এটা achieve করার কিছু way হলো:

- Clear, descriptive variable and function names use করা
- Complex tasks কে smaller, simpler tasks এ break down করা
- Over-engineering solutions avoid করা

**❌**

```javascript
// Not KISS
function calculateAreaOfCircle(radius) {
  let pi = 3.1416;
  let area = pi * radius * radius;
  return area;
}
```

**✅**

```javascript
// KISS
function calculateAreaOfCircle(radius) {
  return Math.PI * radius * radius;
}
```

**✅**

```javascript
// More KISS: Avoids unnecessary variable assignments
function calculateAreaOfCircle(radius) {
  return Math.PI * Math.pow(radius, 2);
}
```

**[⬆ back to top](#table-of-contents)**

## **YAGNI**

YAGNI - "You Ain't Gonna Need It". এই principle code এ unnecessary functionalities add না করার ব্যাপার এ emphasize করে। এটা achieve করার কিছু way হলো:

- এখন যে code টা দরকার, সেই code টা রাখা
- যেই code এখন দরকার নাই, সেটা না রাখা
- যখন new requirement আসবে, তখন code refactor করা
- সকল possible future use case এর পূর্বানুমান না করা

**❌**

```javascript
// Not YAGNI
function calculateTaxes(price, taxRate) {
  let taxes = price * taxRate;
  return price + taxes;
}
```

**✅**

```javascript
// YAGNI
function calculateTax(price, taxRate) {
  return price * taxRate;
}
```

**[⬆ back to top](#table-of-contents)**
