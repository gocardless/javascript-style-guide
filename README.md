# The GoCardless JavaScript styleguide.

This styleguide is for general JavaScript conventions and styles that we follow across all our JavaScript. For Angular specific conventions, refer to the [Angular Style Guide](https://github.com/gocardless/angularjs-style-guide).

## Strings

#### Use single quotes for all strings.

_Why_: in JavaScript there is no difference between single and double quotes. Rather than have a mix throughout a codebase, it is best to pick one and stick to it.

#### Keep line length at around 80 characters.

_Why_: this encourages lines that do less, and to extract variables and functions where a line is longer than required.

We don't have a set 80 char hard limit because sometimes it's more readable to let a line be 85 characters rather than break it up, but in general you should aim for 80 characters or less.

## Asynchronous Code and Promises

#### Ensure that each promise chain has at least one `catch`

_Why_: ensures that error handling is thought of and dealt with in every case.

```javascript
// bad
doSomething().then(...)

// good
doSomething().then(...).catch(...)
```

#### Prefer multiple `then` callbacks over one `then` callback which performs multiple operations

_Why_: this keeps promise chains easier to scan and understand, and keeps the callback functions smaller.

```javascript
// bad
doSomething().then(function(data) {
  // do something
  // do something else
  // do something else else
});

// good
doSomething().then(function(data) {
 // do something
 return data;
}).then(function(data) {
 // do something else
 return data;
}).then(function(data) {
  // and so on
});
```

## Functions

#### Name functions that are given as a callback

_Why_: This helps when debugging by improving errors and stack traces,  and keeps code more self documenting.

```javascript
// bad
createUser().then(function() {
});

// good
createUser().then(function userCreatedSuccesfully() {
});
```

#### Prefer function declarations over function expressions

_Why_: Function declarations are easier to pick out in larger code files.

#### Ensure that the function is defined above its first usage.

_Why_: it can be very confusing to see a function used above its declaration

```javascript
// this will work, but is very unclear
thing();

function thing() {
}
```

#### Prefer guard clauses over nested function bodies

_Why_: indenting an entire function body makes it harder to read and follow. Exiting out early keeps the indent clear and code cleaner.

```javascript
// bad
function doSomethingOnOddNumbers(x) {
  if (x % 2 === 1) {
    // do stuff
  }
};

// good
function doSomethingOnOddNumbers(x) {
  if (x % 2 === 0) return;
  // do stuff
}
```

#### Leave a space after the closing `)` and before the opening `{`, but never before the opening `(`:

```javascript
// bad
function doSomething () {
}

// bad
function doSomething(){
}

// good
function doSomething() {
}
```

#### When a function has arguments that make the line longer than ~80 characters, split it into one parameter per line

_Why_: this is partly to keep lines below 80 characters, but also for readability.

```javascript
// good
function someController(foo, bar, baz) {
}

// bad
function someController(foo, bar, baz, abc, def, ghi, jkl, mno, pqr, stu, vwx, yz) {
}

// good
function someController(
  foo,
  bar,
  baz,
  abc,
  def,
  ghi,
  jkl,
  mno,
  pqr,
  stu,
  vwx,
  yz
) {
}
```

## Objects

#### Leave a trailing comma in object literal definitions

_Why_: makes the diff much clearer when you add a new line, and easier to reorder existing lines without editing them.

```javascript
// bad
{
  foo: 2,
  bar: 3
}

// good
{
  foo: 2,
  bar: 3,
}
```

## Arrays

#### When using array methods such as `forEach`, `map`, etc, pass functions in if possible

_Why_: avoids creating new functions just for the sake of it.

```javascript
function isOdd(x) {
  return x % 2 === 1;
}

// bad
[1, 2, 3].filter(function(x) {
  return isOdd(x);
});

// good
[1, 2, 3].filter(isOdd);
```

#### Leave a trailing comma in Array literal definitions that are over multiple lines

_Why_: it's easier to reorder items and the diff is cleaner when a new item is added

```javascript
// bad
[
  'jack',
  'max'
]

// good
[
  'jack',
  'max',
]
```

## Variables

#### Don't declare a variable within a block

_Why_: it's unclear upon scanning where the variable is defined.

```javascript
// bad
if (x) {
  var y = 2;
} else {
  var y = 3;
}

// good
var y;
if (x) {
  y = 2;
} else {
  y = 3;
}
```

#### Prefer ternary operators for very succinct conditionals which change the initial value of a variable

_Why_: when used with small conditionals, ternary operators are a much more concise way to express them

Do not use a ternary where the condition or either branch of the condition are complex. If in doubt, do not use a ternary operator.

```javascript
var y = x ? 2 : 3;
```

#### Declare one variable per line

_Why_: adding a new variable does not mean other lines need to be edited, and existing lines can be reordered easily.

```javascript
// bad
var x, y, z;

// good
var x;
var y;
var z
```

## Conditionals

#### Always prefer `===` and `!==` when comparing.

_Why_: avoid any bugs with JavaScript's often unintuitive equality rules.

#### Use `==` or `!=` only when checking a variable is `undefined` or `null`:

_Why_: using `===` means checking for both `undefined` and `null`, whereas `==` lets you only check for one.

```javascript
// bad
if (x === undefined || x === null) {}

// good
if (x == null) {}
```

#### Prefer `hasOwnProperty` for checking if a key exists within an object

_Why_: keys that have a falsey value but are still present might slip through the check or result in false positives

```javascript
var thing = { count: 0 };

if (!thing.count) // will evaluate to true, but we don't want it to

if (!thing.hasOwnProperty("count")) // will evaluate to false, which is what we want
```

#### Wrap a conditional in braces, unless it's short enough to fit comfortably on one line.

_Why_: conditionals without braces over multiple lines can easily be misconstrued, only the first line after them is part of the conditional.

```javascript
// bad
if (something)
  doSomethingElse();
  doAnotherThing(); // <-- not part of the conditional!

// good
if (something) {
  doSomethingElse();
  doAnotherThing(); 
}

// bad
if (something) { x = true }

// good
if (something) x = true;
```

#### Leave a space between `if` and the opening `(`:

_Why_: makes it easier to pick out `if`s in a large code file.

```javascript
// bad
if(foo)

// good
if (foo)
```

# ES6 Specific Conventions

## `=>` functions

#### Prefer "fat arrow" functions when the function is small enough to fit on one line

_Why_: they are more concise and readable

```javascript
// bad
[1, 2, 3].map(function(x) { return x * 2 });

// good
[1, 2, 3].map((x) => x * 2);
```

#### Functions that span multiple lines should be function expressions, not arrow functions.

_Why_: arrow functions become harder to follow over multiple lines, and the `return` then becomes explicit.

```javascript
// bad
[1, 2, 3].map((x) => {
  // do lots of things
  // do more things
  return x * 2;
});

// good
[1, 2, 3].map(function(x) {
  // do lots of things
  // do more things
  return x * 2;
});
```

#### Always wrap arrow function arguments in brackets.

_Why_: whilst they are not needed when only taking one argument, it's clearer to just always include them.

```javascript
// bad
[1, 2, 3].map(x => x * 2);

// good
[1, 2, 3].map((x) => x * 2);
```

#### In tests, prefer arrow functions over expressions when defining blocks

_Why_: keeps tests cleaner and easier to pick out the actual assertions

```javascript
// bad
it('does a thing', function() {
});

// good
it('does a thing', () => {
});
```

## Strings

#### Prefer ES6 string interpolation over concatenation

_Why_: easier to follow and less typing of awkward quotes and spaces. Easier to see what the final string will look like.

```js
// bad
var fullName = firstName + " " + lastName;

// good
var fullName = `${firstName} ${lastName}`;
```

## Function Arguments

#### Use the spread operator over converting the `arguments` object:

_Why_: cleaner and doesn't require converting the `arguments` object explicitly.

```javascript
// bad
function foo() {
  var args = [].slice.call(arguments);
}

// good
function foo(...args) {
}
```

## ES6 Modules

#### Only use relative paths when descending, else prefer absolute paths.

_Why_: easier to see where code is coming from when importing it

```javascript
// bad
import {foo} from '../../service/foo';

// good
import {foo} from 'client/app/service/foo';

// good
import {foo} from './foo';
```

## Constants

#### For now, don't use constants

_Why_: they are not properly implemented by Traceur and end up just being `var`s anyway.

#### To define a "constant", define a variable but name it in uppercase:

_Why_: the uppercase convention indicates that the variable should be treated like a constant.

You should __never__ modify a constant, or a variable named like one!

```javascript
// bad
const apiUrl = 'http://example.com';

var API_URL = 'http://example.com';
```

## `let`

#### Do not use the Traceur, 6to5 or similar implementations of `let`

_Why_: they end up being implemented as variables anyway.

