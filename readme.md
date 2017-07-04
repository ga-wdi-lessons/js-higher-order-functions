# Higher Order Functions

## Learning Objectives

- Describe what a higher order function is
- Use higher order functions to iterate over a list
- Describe the uses of `forEach`, `map`, `filter`, and `reduce`
- Define `every` and `some`
- Explain how a custom sort function can be defined

## Review

### What is a function? (10 minutes / 00:10)
- Defined chunk of code that can be called by later code
- Functions are defined with zero or more **paramaters**
  - **Parameters** are special variables available in the body of the function with values assigned by the code calling the function.
  - The values provided by the calling code are called the **arguments** to the function.
- Functions always return a value
  - Either explicitly using a **return** statement
  - Or, implicitly returning `undefined`

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
We call them with **dot notation** because they are methods, an idea we'll dive into further this afternoon.

```js
wdiInstructors.forEach(printInstructorGreeting)
```

#### Map (10 minutes / 00:45)
We discussed functions that were called for their **side effect** versus functions that are called for their **return value** when we introduce functions.
Which is `printInstructorGreeting`?

Frequently, rather than do something for each piece of data, we want to transform each piece of data.

`forEach` has a closely related sibling `map` that instead of calling a helper function for its effect, it calls a helper function for its return value and creates a new array of the return values.

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

Like the others, `filter` is available directly on arrays:

```js
const wdi17one = wdiInstructors.filter(teaches17)

// or with an anonymous function

const wdi17two = wdiInstructors.filter(instructor => instructor.cohor === 17)
```

## Break (10 minutes / 01:05)

#### [Reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) (15 minutes / 01:20)

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

#### Exercise: Defining Some and Every (40 minutes / 02:00)

(30 minutes small groups / 10 minutes class discussion)

- There are two related methods, `some` and `every` that each take a test function (like filter) and applies it to each element of an array.
- `some` will return true if the test function returns true given **any** of the items in the array and false otherwise.
- `every` will return true if the test function returns true for **every** one of the items in the array and false otherwise.
- Working with a partner at your table define funtions `some` and `every` that take an array and a function and work as described.


#### Sort (10 minutes / 2:10)

The `sort` method is another higher order function which gets a test funtion but the sort test is slightly more complicated.

The test function for `sort` is called with two arguments `a` and `b` which represent any two elements being sorted.

### Callbacks (5 minutes / 2:15)

### Returning Functions from Functions

#### Closures (5 minutes / 2:20)

### Review and Questions (10 minutes / 2:30)
