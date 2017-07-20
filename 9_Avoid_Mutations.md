# 9 Avoiding Array Mutations with concat(), slice() and …spread 

[Lesson Video](https://egghead.io/lessons/javascript-redux-avoiding-array-mutations-with-concat-slice-and-spread)
|| Full example [here](https://github.com/huiwenhw/learn-redux/blob/master/9_Avoid_Mutations.html)

In this lesson, we’ll use the [Expect Library](https://github.com/mjackson/expect) to test assertions and [deepFreeze](https://github.com/substack/deep-freeze) to make sure that our code is free from mutations 

**Lesson objective: Learn to avoid mutations in Redux**

We want to build a count release application - we’ll need the Add Counter, Remove Counter and Increment Counter methods. 

**TLDR**
* Use concat instead of push 
* Use slice and concat instead of splice 
* Use spread syntax to write it more concisely 


**Add Counter method**

* Appends 0 at end of past array 
* Push modifies the original array. Using deepFreeze, we know that the attempt to push does not work.
```
const addCounter = (list) => {
  list.push(0);
  return list;
};

const testAddCounter = () => {
  const listBefore = [];
  const listAfter = [0];
 
  deepFreeze(listBefore);
  
  expect(
     addCounter(listBefore)
  ).toEqual(listAfter);
};

testAddCounter();
console.log('All tests passed!');

Error:
"TypeError: Cannot add property 0, object is not extensible
    at Array.push (<anonymous>)
    at addCounter (todadelipu.js:4:8)
    at testAddCounter (todadelipu.js:14:10)
    at todadelipu.js:17:1"
```
* Instead of push, we’re gonna use the concat method, which does not modify the original array 
```
const addCounter = (list) => {
  return list.concat([0]);
};
```
* Using the ES6 spread operator to write the same code in a more concise way 
```
const addCounter = (list) => {
  return [...list, 0];
};
```


**Next, we’ll add a remove counter method**

* Takes in a array of numbers and the index to skip in the array 
* Usually, we use the splice method but that changes the original array 
```
const removeCounter = (list, index) => {
  list.splice(index, 1);
  return list;
};

const testRemoveCounter = () => {
  const listBefore = [0, 10, 20];
  const listAfter = [0, 20];
  
  deepFreeze(listBefore);
  
  expect(
    removeCounter(listBefore, 1)
  ).toEqual(listAfter);
};

testRemoveCounter();
console.log('All tests passed!');

Error: 
"TypeError: Cannot add/remove sealed array elements
    at Array.splice (<anonymous>)
    at removeCounter (todadelipu.js:11:8)
    at testRemoveCounter (todadelipu.js:30:10)
    at todadelipu.js:34:1"
```
* slice gives a part of the array from the beginning to the ending index 
* We’re taking the parts before index and after index and concatenating them to get a new array 
```
const removeCounter = (list, index) => {
  return list
    .slice(0, index)
    .concat(list.slice(index+1));
};
```
* using the ES6 array spread operator 
```
const removeCounter = (list, index) => {
  return [
    ...list.slice(0, index),
    ...list.slice(index+1)
  ];
};
```


**Next, we add a increment counter method**

* This takes in a list of numbers and index at which to increment by 1
* Directly setting the index works, but its mutating the array 
```
const incrementCounter = (list, index) => {
  list[index]++;
  return list;
};

const testIncrementCounter = () => {
  const listBefore = [0, 10, 20];
  const listAfter = [0, 11, 20];
  
  deepFreeze(listBefore);
  
  expect(
    incrementCounter(listBefore, 1)
  ).toEqual(listAfter);
};

testIncrementCounter();
console.log('All tests passed!');

Error:
"Error: Expected [ 0, 10, 20 ] to equal [ 0, 11, 20 ]
    at a (https://wzrd.in/standalone/expect@latest:2:6220)
    at t.value (https://wzrd.in/standalone/expect@latest:1:26228)
    at testIncrementCounter (todadelipu.js:43:43)
    at todadelipu.js:48:1"
```
* We can use slice and concat which we did before for removeCounter 
* We take the parts before index, concat it with index+1 and take the parts after index 
```
const incrementCounter = (list, index) => {
  return list 
    .slice(0, index)
    .concat([list[index]+1])
    .concat(list.slice(index + 1));
};
```
* Using ES6 spread syntax 
```
const incrementCounter = (list, index) => {
  return [
    ...list.slice(0, index),
    list[index]+1,
    ...list.slice(index+1)
  ];
};
```
