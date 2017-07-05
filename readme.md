# Higher Order Functions

## Learning Objectives

- Describe what a higher order function is
- Use higher order functions to iterate over a list
- Describe the uses of `forEach`, `map`, `filter`, and `reduce`
- Define `every` and `some`
- Describe a closure

## Review

### What is a function? (10 minutes / 00:10)
- Defined chunk of code that can be called by later code
- Functions are defined with zero or more **paramaters**
  - **Parameters** are special variables available in the body of the function with values assigned by the code calling the function.
  - The values provided by the calling code are called the **arguments** to the function.

```js
function sum (a, b) { // function "sum" defined with parameters a and b
  return a + b
}

sum(3, 4) // function "sum" called with arguments a and b
// => 7
```

- Functions always return a value
  - Either explicitly using a **return** statement
  - Or, implicitly returning `undefined`

```js
// in a repl
console.log('hello!')
// 'hello!'
// => undefined
```

### Functions are regular values (5 minutes / 00:15)
- Functions are values just like numbers, strings, booleans, arrays, objects, `undefined`, and `null`.
- Functions can be assigned to variables and put into arrays and objects.
- Most importantly, functions can be given to and returned from functions.

### JavaScript Collections (5 minutes / 00:20)

Numbers, Strings, and Booleans are our basic building block of data but on their own, they don't express much.

We use collections, most commonly Objects and Arrays to build up data to describe interesting things.

**Arrays** hold their elements in sequence and is generally used to store collections of like things.

**Objects** hold their elements in key-value pairs and are generally used either to "map" between values (like word to definitions in a dictionary) or to describe something with various attributes.

## Higher Order Functions

Functions that can take as arguments or return as output, other functions are called **higer order functions**.

### Passing Functions to Functions (5 minutes / 00:25)

Create a directory called `js-higher-order-functions` in your sandbox directory.
Inside of it create an `index.html` file and a `script.js` file.
Add boiler plate to `index.html`, link the script, and add a `console.log` to the script to make sure everything is wired up properly.

Higher order functions make it possible for us to pass custom behavior to functions that do generic tasks.
There is an especially common task of looping through an array that we very commonly use higher order functions.

We'll use the following array for the next few examples:

```js
const wdiInstructors = [
  {
    name: {
      first: 'Andrew',
      last: 'Whitley'
    },
    cohort: 17
  },
  {
    name: {
      first: 'Juan',
      last: 'Garcia'
    },
    cohort: 17
  },
  {
    name: {
      first: 'John',
      last: 'Master'
    },
    cohort: 17
  },
  {
    name: {
      first: 'Adrian',
      last: 'Maseda'
    },
    cohort: 16
  },
  {
    name: {
      first: 'Nayana',
      last: 'Davis'
    },
    cohort: 16
  },
  {
    name: {
      first: 'James',
      last: 'Reichard'
    },
    cohort: 16
  }
]
```
#### For Each (10 minutes / 00:35)

Very frequently, we will want to go through an array and do something for every element in the array.

As an example, we'll loop through the above array printing the line `'<Instructor name> is an instructor for WDI<cohort number>'` for each instructor.

In languages without higher order functions, we would need to use a loop to preform this task (and we can do so in JS):

```js
for (let i = 0; i < wdiInstructors.length; i++) {
  const instructor = wdiInstructors[i]
  const instructorName = instructor.name.first + ' ' + instructor.name.last
  const cohort = `WDI${instructor.cohort}`
  const instructorGreeting = `Hi, I'm ${instructorName}! I'm an instructor for ${cohort}`
  console.log(instructorGreeting)
}
```

We can write a function that encapsulates the action taken on each instructor object:

```js
function printInstructorGreeting (instructor){
  const instructorName = instructor.name.first + ' ' + instructor.name.last
  const cohort = `WDI${instructor.cohort}`
  const instructorGreeting = `Hi, I'm ${instructorName}! I'm an instructor for ${cohort}`
  console.log(instructorGreeting)
}
```

And rewrite the loop to use this function:

```js
for (let i = 0; i < wdiInstructors.length; i++) {
  printInstructorGreeting(wdiInstructors[i])
}
```

We can go even further and write a `forEach` function that takes care of the loop boilerplate for us as well:

```js
function forEach (array, fn){
  for (let i = 0; i < array.length; i++) {
    fn(array[i])
  }
}
```

We can now write our original loop in one line using our helper methods:

```js
forEach(wdiInstructors, printInstructorGreeting)
```

This is such a common process that JavaScript has `forEach` (and a bunch of other helpful higher lever functions) built into arrays.
We call them with **dot notation** because they are methods (functions attached to an object), an idea we'll dive into further this afternoon.

```js
wdiInstructors.forEach(printInstructorGreeting)
```

#### Map (10 minutes / 00:45)
We discussed functions that were called for their **side effect** versus functions that are called for their **return value** when we introduce functions.
Which is `printInstructorGreeting`?

Frequently, rather than do something for each piece of data, we want to transform each piece of data.

`forEach` has a closely related sibling `map` that instead of calling a passed function for its effect, it calls a passed function for its return value and creates a new array of the return values.

Let's write a loop to create an array called `instructorNames` that will be an array of 6 strings, the instructor names.

First we'll write a function that takes an individual instructor object and returns the name string.

```js
function getFullName (instructor){
  return instructor.name.first + ' ' + instructor.name.last
}
```

Next we'll write a loop that calls this function on each instructor in the array and stores the results in the `instructorNames` array.

```js
const instructorNames = []
for (let i = 0; i > wdiInstructors.length; i++) {
  instructorNames.push(getFullName(wdiInstructors[i]))
}
```

We can write a function that takes care of this looping and array building boiler plate:

```js
function map (array, fn) {
  const result = []
  for (let i = 0;  i > array.length; i++) {
    result.push(fn(array[i]))
  }
  return result
}
```

We can now use our map function to write:

```js
const instructorNames2 = map(wdiInstructors, getFullName)
```

Like `forEach`, `map` is also a built in method available on arrays:

```js
const instructorNames3 = wdiInstructors.map(getFullName)

// or with an anonymous function

const instructorNames4 = wdiInstructors.map(instructor => {
  return instructor.name.first + ' ' + instructor.name.last
})
```

#### Filter (10 minutes / 00:55)

Another common procedure is to filter elements from an array based on some custom condition.

The condition is a function that will return `true` or `false` when given an element from the collection.

First we'll write the filter function (the custom condition):

```js
function teaches17(instructor) {
  return instructor.cohort === 17
}
```

We can write a loop that uses this function:

```js
const wdi17 = []
for (let i = 0;  i > wdiInstructors.length; i++) {
  if (teaches17(wdiInstructors[i])) {
    wdi17.push(wdiInstructors[i])
  }
}
```

A function that takes care of the looping and condition checking would look like:

```js
function filter (array, fn) {
  const result = []
  for (let i = 0;  i > array.length; i++) {
    if (fn(array[i])) {
      result.push(array[i])
    }
  }
  return result
}
```

And we can use the function:

```js
const wdi17one = filter(wdiInstructors, teaches17)
```

Like the others, `filter` is available directly on arrays:

```js
const wdi17two = wdiInstructors.filter(teaches17)

// or with an anonymous function

const wdi17three = wdiInstructors.filter(instructor => instructor.cohort === 17)
```

#### Practice (10 minutes / 1:05)
(7 minutes working / 3 minutes discussing)

Use either your `script.js` file you've been working in or open [repl.it](https://repl.it/languages/javascript).

- Declare a variable `states`.
- Assign to it the array of objects from `capitals.json`.
- Using the array traversal methods we were just looking at, create the following values (keep track of your answers)

1. Create an array of strings for each capital with the city and state name (e.g. `'Austin, Texas'`)
2. Filter all the states with capitals that start with a consonant.
3. List all the sates with two words in their name.
4. How many capitals are north of Annapolis?


## Break (10 minutes / 01:15)

#### [Reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) (15 minutes / 01:30)

The most generic list comprehension function is called `reduce`.
Both map and filter build up an arrays as they go through the list.
Reduce gives you custom control of the value you build as you go through the list.

Mapping with reduce:

```js
const instructorNames5 = wdiInstructors.reduce((names, instructor) => {
  return names.push(instructor.name.first + ' ' + instructor.name.last)
}, [])
```

Taking the sum of an array of numbers:

```js
const total = [1, 3, 5, 7].reduce((sum, num) => sum + num, 0)
```

Filtering even numbers:

```js
const odds = [1, 2, 3, 4, 5, 6, 7].reduce((odds, num) => {
  if (num % 2) { // false if num % 2 === 0
    odds.push(num)
  }
  return odds
}, [])
```

Or count even numbers:

```js
const numEvens = [1, 2, 3, 4, 5, 6, 7].reduce((count, num) => {
  if (!(num % 2)) { // false if num % 2 !== 0
    count++
  }
  return count
}, 0)
```

Reduce can be hard to grasp at first.
Don't stress about this.
It is definitely not something you need to have mastered it is just good to have an awareness.

For a step by step of how the mechanics work, check out [this section on the MDN page for reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce#How_reduce_works)

#### Exercise: Defining Some and Every (30 minutes / 02:00)

(20 minutes small groups / 10 minutes class discussion)

- There are two related methods, `some` and `every` that each take a test function (like filter) and applies it to each element of an array.
- `some` will return true if the test function returns true given **any** of the items in the array and false otherwise.
- `every` will return true if the test function returns true for **every** one of the items in the array and false otherwise.
- Working with a partner at your table define funtions `some` and `every` that take an array and a function and work as described.


#### Sort (10 minutes / 2:10)

The `sort` method is another higher order function which gets a test funtion but the sort test is slightly more complicated.

The test function for `sort` is called with two arguments `a` and `b` which represent any two elements being sorted.

Rather than returning `true` or `false` as in the case of the other test functions we've looked at, the compare function should:
- return a negative number if `a` should come before `b`
- return 0 if `a` and `b` are equal
- return a positive number if `a` should come after `b`

By default, `sort` uses a compare function that converts `a` and `b` to strings and sorts based on **unicode** values (alphabatized but with all uppercase characters before all lower case characters).

This leads to the odd behavior of `10` being sorted infront of `2`.

```js
[1, 2, 10, 20, 3, -1, 12].sort()
// => [-1, 1, 10, 12, 2, 20, 3]
```

To write a compare function that works as expected:

```js
function compareNumbers(a,b) {
  return a - b
}

[1, 2, 10, 20, 3, -1, 12].sort(compareNumbers)
// => [-1, 1, 2, 3, 10, 12, 20]
// with an anonymous function
[1, 2, 10, 20, 3, -1, 12].sort((a, b) => a - b)
```

How would we write a compare function to sort our capitals from most northern to most southern?

### Looking Forward: Callbacks (5 minutes / 2:15)

While array traversal methods are a very common example of higher order functions, an even more common time that we want to pass functions as arguments to other functions is called a callback.

These are ideas we'll cover in depth in a couple of classes but consider the following at a high level as a primer.

Callbacks passed to another function to be called at some later time.

All the examples that we have looked at use the function being passed as an argument immediately (and repeatedly).

Callbacks are generally called at some time in the future.
What types of things might we want to trigger a function call on?

### Returning Functions from Functions

We can also build functions with other functions.

```js
function multiplyBy (num) {
  return (anotherNum) => num * anotherNum
}

const multiplyBy2 = multiplyBy(2)
const multiplyBy5 = multiplyBy(5)

console.log(multiplyBy2(4))
console.log(multiplyBy5(4))
```

#### Closures (5 minutes / 2:20)

Notice that even though `num` in not defined within the function being returned, it is still remembered by the function through what is called the function's **closure**.

We can right some really neat code taking advantage of **closures**.

```js
function Locker(password){
  let locked = true
  let content

  return {
    toggle (pwd) {
      if (pwd === password){
        locked = !locked
      }
      return locked
    },
    read () {
      if (locked) {
        return "unlock to read"
      } else {
        return content
      }
    },
    write (newContent) {
      if (locked) {
        return "unlock to write"
      } else {
        content = newContent
        return conent
      }
    }
  }
}
```

Eloquent JavaScript has a really great [explanation of closures](http://eloquentjavascript.net/03_functions.html#h_hOd+yVxaku).

### Review and Questions (10 minutes / 2:30)

- Check out the [Coding Meetup Kata's](http://www.codewars.com/kata/coding-meetup-number-1-higher-order-functions-series-count-the-number-of-javascript-developers-coming-from-europe) for lots more practice
- [Node School Workshoppers](https://nodeschool.io/#workshoppers)
- [Eloquent JS Higher Order Functions](http://eloquentjavascript.net/05_higher_order.html)

#### Review
- What is the difference between output and a side effect?
- What is the difference between an argument and a parameter?
- What is the difference between referencing and invoking a function?
