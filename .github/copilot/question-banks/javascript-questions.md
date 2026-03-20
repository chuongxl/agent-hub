# JavaScript Interview Question Bank

A curated collection of JavaScript interview questions organized by difficulty and topic for the Interview Simulator skill.

---

## 1. Scope, Closures & Hoisting

### Foundational Questions

**Q1.1**: "Can you explain what a closure is and provide a simple example?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Closure definition and practical use
- **Follow-up** (if needed): "Why would you use a closure in a real application?"

**Q1.2**: "What's the difference between function scope and block scope in JavaScript?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: var vs. let/const, scope chains
- **Follow-up**: "How does hoisting work with var, let, and const?"

**Q1.3**: "What gets hoisted in JavaScript, and what's the difference between hoisting for functions and variables?"
- **Expected Level**: Intermediate
- **Concept**: Hoisting mechanics, temporal dead zone
- **Follow-up**: "What happens if you try to access a variable in the temporal dead zone?"

### Intermediate Questions

**Q1.4**: "Write a function that creates a counter with private variables. The counter should increment, decrement, and return the current value."
- **Expected Level**: Intermediate
- **Concept**: Closures for data privacy, state management
- **Follow-up**: "How would you prevent someone from resetting the counter directly?"

**Q1.5**: "Explain what happens in this code and predict the output:"
```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
```
- **Expected Level**: Intermediate
- **Concept**: Closure iteration issues, var scope
- **Follow-up**: "How would you fix this to print 0, 1, 2 instead?"

### Advanced Questions

**Q1.6**: "What's the temporal dead zone, and why does it exist? Demonstrate it with an example."
- **Expected Level**: Advanced
- **Concept**: TDZ mechanics, let/const initialization
- **Follow-up**: "How does the JS engine handle this differently than hoisting?"

**Q1.7**: "Explain module pattern and IIFE (Immediately Invoked Function Expression). When would you use them today?"
- **Expected Level**: Advanced
- **Concept**: Closures for encapsulation, data privacy patterns
- **Follow-up**: "How do ES6 modules compare to the module pattern?"

---

## 2. Prototypes & Inheritance

### Foundational Questions

**Q2.1**: "What is a prototype in JavaScript, and how does the prototype chain work?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Prototype basics, inheritance hierarchy
- **Follow-up**: "How does looking up a property differ from looking up a method?"

**Q2.2**: "What's the difference between `__proto__` and the `prototype` property?"
- **Expected Level**: Intermediate
- **Concept**: Internal slots vs. constructor property
- **Follow-up**: "Why shouldn't you use `__proto__` directly?"

### Intermediate Questions

**Q2.3**: "Write a simple inheritance example using constructor functions and the `prototype` property."
- **Expected Level**: Intermediate
- **Concept**: Constructor-based inheritance
- **Follow-up**: "What problem does this approach have? How would you fix it?"

**Q2.4**: "What's the difference between classical inheritance (constructor functions) and prototypal inheritance?"
- **Expected Level**: Intermediate
- **Concept**: Inheritance patterns, paradigm differences
- **Follow-up**: "Which is more flexible? Why?"

### Advanced Questions

**Q2.5**: "Explain the 4 ways to create objects in JavaScript and their pros/cons."
- **Expected Level**: Advanced
- **Concept**: Object creation patterns, performance implications
- **Follow-up**: "Which would you use for what scenario?"

**Q2.6**: "What are the differences between `Object.create()`, constructor functions, and ES6 classes? When would you use each?"
- **Expected Level**: Advanced
- **Concept**: Modern patterns, performance, readability
- **Follow-up**: "Are ES6 classes truly classes or syntactic sugar?"

---

## 3. Async JavaScript

### Foundational Questions

**Q3.1**: "What's the difference between callbacks, promises, and async/await?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Async patterns evolution
- **Follow-up**: "Which do you prefer and why?"

**Q3.2**: "What does `new Promise()` return, and what are its three states?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Promise states, executor function
- **Follow-up**: "What happens if the executor throws an error?"

**Q3.3**: "Write a promise that resolves after 2 seconds with a value of 'done'."
- **Expected Level**: Beginner
- **Concept**: Promise constructor syntax
- **Follow-up**: "How would you handle rejection?"

### Intermediate Questions

**Q3.4**: "Explain `.then()`, `.catch()`, and `.finally()`. When would you use each?"
- **Expected Level**: Intermediate
- **Concept**: Promise chaining, error handling, cleanup
- **Follow-up**: "Does `.finally()` have access to the resolved value?"

**Q3.5**: "What are `Promise.all()`, `Promise.race()`, and `Promise.allSettled()`? When would you use each?"
- **Expected Level**: Intermediate
- **Concept**: Promise combinators, error handling
- **Follow-up**: "How does error handling differ between them?"

**Q3.6**: "Convert this callback-based function to a promise:"
```javascript
function fetchUser(id, callback) {
  setTimeout(() => callback(null, { id, name: 'John' }), 1000);
}
```
- **Expected Level**: Intermediate
- **Concept**: Promisification, wrapper functions
- **Follow-up**: "How would you handle errors in the original callback?"

### Advanced Questions

**Q3.7**: "What's the difference between promise chaining and async/await at the bytecode level?"
- **Expected Level**: Advanced
- **Concept**: Performance, implementation details
- **Follow-up**: "Are there cases where promises are better than async/await?"

**Q3.8**: "Implement a utility that retries a failed promise N times with exponential backoff."
- **Expected Level**: Advanced
- **Concept**: Error handling, advanced promise patterns
- **Follow-up**: "How would you cancel a retry in progress?"

---

## 4. Event Loop & Microtask Queue

### Foundational Questions

**Q4.1**: "How does the JavaScript event loop work at a high level?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Event loop phases, blocking behavior
- **Follow-up**: "What blocks the event loop?"

**Q4.2**: "What's the difference between the callback queue (macrotask) and the microtask queue?"
- **Expected Level**: Intermediate
- **Concept**: Task types, execution order
- **Follow-up**: "Give examples of what goes in each queue."

### Intermediate Questions

**Q4.3**: "Predict the output and explain the order of execution:"
```javascript
console.log('Start');
Promise.resolve().then(() => console.log('Promise'));
setTimeout(() => console.log('Timeout'), 0);
console.log('End');
```
- **Expected Level**: Intermediate
- **Concept**: Event loop ordering, microtasks vs. macrotasks
- **Follow-up**: "Why does Promise execute before setTimeout?"

**Q4.4**: "If microtasks run before macrotasks, can a microtask block the event loop?"
- **Expected Level**: Intermediate
- **Concept**: Event loop blocking, performance implications
- **Follow-up**: "What could happen if a `.then()` callback takes a long time?"

### Advanced Questions

**Q4.5**: "Explain what happens with nested promises and microtasks:"
```javascript
Promise.resolve()
  .then(() => {
    console.log('1');
    return Promise.resolve();
  })
  .then(() => console.log('2'))
  .then(() => console.log('3'));
```
- **Expected Level**: Advanced
- **Concept**: Microtask queuing, promise resolution order
- **Follow-up**: "Why does there seem to be a delay between 1 and 2?"

---

## 5. ES6+ Features

### Foundational Questions

**Q5.1**: "What is destructuring, and can you give examples of array and object destructuring?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Destructuring syntax, use cases
- **Follow-up**: "How do you set default values in destructuring?"

**Q5.2**: "Explain the spread operator (`...`) and the rest parameter. What's the difference?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Spread/rest syntax, array/object operations
- **Follow-up**: "How do they differ in function parameters vs. literals?"

### Intermediate Questions

**Q5.3**: "What's the difference between `const` and `let`? When would you use each?"
- **Expected Level**: Intermediate
- **Concept**: Block scope, reassignment rules, best practices
- **Follow-up**: "Can you reassign or redeclare a `const`? What about mutate it?"

**Q5.4**: "Write an arrow function vs. a regular function. What are the key differences?"
- **Expected Level**: Intermediate
- **Concept**: Arrow functions, `this` binding, implicit returns
- **Follow-up**: "When should you NOT use arrow functions?"

### Advanced Questions

**Q5.5**: "Explain template literals and tagged templates. When would you use tagged templates?"
- **Expected Level**: Advanced
- **Concept**: String interpolation, custom parsing
- **Follow-up**: "Give a real-world example of a tagged template."

**Q5.6**: "What are Proxy and Reflect? When would you use them?"
- **Expected Level**: Advanced
- **Concept**: Metaprogramming, object interception
- **Follow-up**: "Can you implement a simple reactive object using Proxy?"

---

## 6. Common Pitfalls & Anti-Patterns

### Foundational Questions

**Q6.1**: "What's the difference between `==` and `===`? When should you use each?"
- **Expected Level**: Beginner
- **Concept**: Type coercion, equality rules
- **Follow-up**: "Give examples where they differ."

**Q6.2**: "What's the `this` keyword, and what does it refer to in different contexts?"
- **Expected Level**: Intermediate
- **Concept**: `this` binding, context awareness
- **Follow-up**: "How do arrow functions change `this` binding?"

### Intermediate Questions

**Q6.3**: "What's the difference between null, undefined, and undeclared variables?"
- **Expected Level**: Intermediate
- **Concept**: Variable states, error handling
- **Follow-up**: "How do you check for each?"

**Q6.4**: "Predict the output:"
```javascript
const obj = {
  value: 42,
  getValue: function() {
    return this.value;
  }
};
const fn = obj.getValue;
console.log(fn());
```
- **Expected Level**: Intermediate
- **Concept**: `this` binding in method calls
- **Follow-up**: "How would you fix this?"

### Advanced Questions

**Q6.5**: "What are common memory leak patterns in JavaScript? How do you prevent them?"
- **Expected Level**: Advanced
- **Concept**: Garbage collection, event listeners, circular references
- **Follow-up**: "How would you debug memory leaks?"

**Q6.6**: "Explain the difference between shallow copy and deep copy. When do you need each?"
- **Expected Level**: Advanced
- **Concept**: Object cloning, immutability
- **Follow-up**: "How would you deep clone an object with methods and circular references?"

---

## Usage Guidelines

- **Mix difficulty levels** in an interview (start foundational, progress to advanced)
- **Combine related questions** (e.g., closures + hoisting in one session)
- **Use follow-ups** to probe deeper understanding
- **Adapt based on responses** (skip if too easy, provide hints if too hard)
- **Ask "why"** frequently to assess understanding vs. memorization

---

## Related Resources

- MDN Web Docs: https://developer.mozilla.org/en-US/docs/Web/JavaScript
- JavaScript.info: https://javascript.info/
- You Don't Know JS (Kyle Simpson)
- Eloquent JavaScript (Marijn Haverbeke)
