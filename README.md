# The GoCardless JavaScript style guide

This style guide is for general JavaScript conventions and styles that we follow across all our JavaScript. For Angular specific conventions, refer to the [Angular Style Guide](https://github.com/gocardless/angularjs-style-guide).

## Browser Support

The projects we work on support the following browsers:

- Chrome 39+
- Safari 7+
- Firefox 33+
- IE10+

Be aware of this when reading the guide - some of the JS shown here may not work in older versions.

## Strings

#### Use single quotes for all strings.

_Why_: in JavaScript there is no difference between single and double quotes. Rather than have a mix throughout a code base, pick one and stick to it.

#### Keep line length at around 80 characters.

_Why_: this encourages developers to write lines that do less, and extract variables and functions where a line is longer than required.

We don’t have an 80 char hard limit because sometimes it’s more readable to let a line be 85 characters rather than break it up, but in general you should aim for 80 characters or less.

#### Use `String.prototype.includes` to check for substrings

_Why_: it's intent is much clearer than `indexOf`, and it is included as part of the ES6 specification

In a JS project that is transpiled using Traceur, Babel or similar, use the new `String.prototype.includes` method:

```js
// bad
if (str.indexOf('foo') > -1) {
}

// good
if (str.includes('foo')) {
}
```

## Async

#### Ensure that each promise chain has at least one `catch`.

_Why_: ensures that errors are always handled.

```js
// bad
doSomething().then(...)

// good
doSomething().then(...).catch(...)
```

#### Prefer multiple `then` callbacks over one `then` callback which performs multiple operations.

_Why_: this keeps promise chains easier to read, and keeps callback functions smaller.

```js
// bad
doSomething().then(function processSomething(data) {
  // do something
  // do something else
  // do something else else
});

// good
doSomething().then(function processSomething(data) {
 // do something
 return data;
}).then(function somethingElse(data) {
 // do something else
 return data;
}).then(function anotherThing(data) {
  // and so on
});
```

## Functions

#### Name functions that are given as a callback.

_Why_: the function name replaces “anonymous function” in stack traces, which makes debugging easier. Naming functions also helps code be self documenting.

```js
// bad
createUser().then(function() {
});

// good
createUser().then(function userCreatedSuccesfully() {
});
```

#### Prefer function declarations to function expressions.

_Why_: function declarations are easier to pick out in larger code files.

#### Ensure that the function is defined above its first usage.

_Why_: hoisting enables JavaScript files to have their most important content at the top. However, we’re used to reading top to bottom and left to right, so this can be confusing.

```js
// this will work but is unclear
thing();

// more code

function thing() {
}
```

#### Prefer guard clauses to nested function bodies.

_Why_: indenting an entire function body makes it harder to read. Providing a base case early keeps the code cleaner.

```js
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

#### Leave a space after the closing `)` and before the opening `{`, but never before the opening `(`.

```js
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

#### When a function has arguments that make the line longer than ~80 characters, split it into one parameter per line.

_Why_: keeps lines below 80 characters, after which readability declines.

```js
// good
function SomeController(foo, bar, baz) {
}

// bad
function SomeController(foo, bar, baz, abc, def, ghi, jkl, mno, pqr, stu, vwx, yz) {
}

// good
function SomeController(
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

#### When an object literal has more than one property, split it into one property per line, leaving a trailing comma.

_Why_: keeps lines below 80 characters, after which readability declines. Trailing commas make it easier to reorder existing lines without editing them, and makes new lines clearer in the diff.

```js
// good
{ foo: 2 }

// bad
{ foo: 2, bar: 3 }

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

#### When using array methods such as `forEach`, `map`, etc, pass function names in if possible.

_Why_: avoids unnecessary function wrapping.

```js
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

#### Never mutate the array or objects within an array in a `forEach`, `map` or similar.

_Why_: JavaScript's object mutation can be very implicit and easy to miss. It's much better to have slightly more verbose but much clearer code.

```js
// bad -  payments array has been mutated, not obvious
payments.forEach(function(payment) {
  payment.id = 'ABC123'
});

// bad - the original payments array is mutated
payments.map(function(payment) {
  payment.id = 'ABC123';
});

// good - don't mutate the array, but create and modify a new one:
payments = _.cloneDeep(payments).map(function(payment) {
  payment.id = 'ABC123';
  return payment;
});
```

In practice we may not use `_.cloneDeep` - alter the above code as appropriate based on the libraries available.

#### Leave a trailing comma in array literals that span multiple lines.

_Why_: it’s easier to reorder items and the diff is cleaner when a new item is added.

```js
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

#### Prefer LoDash's `_.includes` method to test for Array inclusion

_Why_: its intent is much clearer than using `indexOf`.

If LoDash is available and included in the project, `_.includes` is much easier to read and cleaner to use than `indexOf`.

```js
// bad
if (names.indexOf('jack') > -1) {
}

// good
if (_.includes(names, 'jack')) {
}
```

## Variables

#### Don’t declare a variable within a block.

_Why_: it’s unclear where the variable is defined.

```js
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

#### Use ternary operators for very succinct conditionals which change the initial value of a variable.

_Why_: ternary operators are concise when used with small conditionals.

Use an `if` statement if any branch of the condition is complex.

```js
var y = x ? 2 : 3;
```

#### One variable per line.

_Why_: adding a new variable does not mean other lines need to be edited, and existing lines can be reordered easily.

```js
// bad
var x, y, z;

// good
var x;
var y;
var z;
```

## Conditionals

#### Always prefer `===` and `!==` when comparing.

_Why_: eliminates errors caused by JavaScript’s complex equality and coercion rules.

#### Use `==` or `!=` only when checking a variable is `undefined` or `null`:

_Why_: using `==` checks for both `undefined` and `null`, whereas `===` only checks for one.

```js
// bad
if (x === undefined || x === null) {}

// good
if (x == null) {}
```

#### Prefer `hasOwnProperty` for checking if a key exists within an object.

_Why_: keys that have a falsey value but are still present might result in false positives.

```js
var thing = { count: 0 };

if (!thing.count) // will evaluate to true, but we don’t want it to

if (!thing.hasOwnProperty('count')) // will evaluate to false, which is what we want
```

#### Wrap a conditional in braces if it’s > 1 line long.

_Why_: conditionals without braces over multiple lines can easily be misconstrued.

```js
// bad
if (something) { x = true }

// good
if (something) x = true;

// bad
if (something)
  doSomethingElse();
  doAnotherThing(); // <- not part of the conditional!

// good
if (something) {
  doSomethingElse();
  doAnotherThing(); 
}
```

#### Leave a space between `if` and the opening `(`.

_Why_: makes it easier to pick out `if`s in a large code file.

```js
// bad
if(foo)

// good
if (foo)
```

# ES6 Specific Conventions

## `=>` functions

#### Prefer “fat arrow” functions when the function is small enough to fit on one line, or when the function’s job is to return the result of an expression.

_Why_: they are more concise and readable, and implicitly return when the body is an expression.

```js
// bad
[1, 2, 3].map(function(x) { return x * 2 });

// good
[1, 2, 3].map((x) => x * 2);
```

#### Use function expressions when the function body is a block.

_Why_: an arrow function that spans multiple lines requires a block and an explicit return statement. Therefore, using an arrow function offers a minimally shorter expression at the expense of losing the function name in a stack trace.

```js
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

_Why_: they aren’t needed when the function only takes one parameter, but it’s clearer to always include them.

```js
// bad
[1, 2, 3].map(x => x * 2);

// good
[1, 2, 3].map((x) => x * 2);
```

#### In tests prefer arrow functions to function expressions.

Unfortunately our e2e tests run in Node.js, so don't support arrow functions (yet). When they do, we'll prefer to use them there too.

_Why_: keeps tests cleaner and easier to pick out the actual assertions.

```js
// bad
it('does a thing', function() {
});

// good
it('does a thing', () => {
});
```

## Strings

#### Prefer ES6 string interpolation to concatenation.

_Why_: easier to read.

```js
// bad
var fullName = firstName + ' ' + lastName;

// good
var fullName = `${firstName} ${lastName}`;
```

## Function Arguments

#### Use the spread operator over converting the `arguments` object.

```js
// bad
function foo() {
  var args = [].slice.call(arguments);
}

// good
function foo(...args) {
}
```

## ES6 Modules

#### Use relative paths when the target is a descendant of the path declaration, else prefer absolute paths.

_Why_: easier to see a file’s position, and prevents many parent directory operators.

```js
// bad
import {foo} from '../../service/foo';

// good
import {foo} from 'client/app/service/foo';

// good
import {foo} from './foo';
```

## Constants

#### For now, don’t use `const`.

_Why_: they are not properly implemented by Traceur and end up just being `var`s.

#### To indicate a variable should be treated like a constant, use upper snake case.

_Why_: the uppercase convention indicates that the variable should be treated as a constant.

You should __never__ modify a constant, or a variable named like one!

```js
// bad
const apiUrl = 'http://example.com';

// good
var API_URL = 'http://example.com';

// very bad!
API_URL = API_URL + '/new';
```

## `let`

#### Do not use the Traceur, 6to5 or similar implementations of `let`.

_Why_: similarly to `const`, they end up being implemented as variables.

