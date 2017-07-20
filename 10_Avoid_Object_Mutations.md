# 10 Avoid Object Mutations with Object.assign() and …spread 

[Lesson Video](https://egghead.io/lessons/javascript-redux-avoiding-object-mutations-with-object-assign-and-spread)
|| Full example [here](https://github.com/huiwenhw/learn-redux/blob/master/9_10_Avoid_Mutations.html)

**Lesson objective: Learn to avoid mutations in Redux**

We want to create a function called toggle todo that takes our todo object and flips its completed field 

**Toggle todo**
* Flipping its field by using ! 
* But… it’s not allowed cause its mutating the original object 
```Javascript
const toggleTodo = (todo) => {
  todo.completed = !todo.completed;
  return todo;
};

const testToggleTodo = () => {
  const todoBefore = {
    id: 0,
    text: 'Learn Redux', 
    completed: false
  };
  const todoAfter = {
    id: 0, 
    text: 'Learn Redux', 
    completed: true
  };

  deepFreeze(todoBefore);

  expect(
    toggleTodo(todoBefore)
  ).toEqual(todoAfter);
};

testToggleTodo();
console.log('All tests passed!')

Error:
"Error: Expected { completed: false, id: 0, text: 'Learn Redux' } to equal { completed: true, id: 0, text: 'Learn Redux' }
at a (https://wzrd.in/standalone/expect@latest:2:6220)
at t.value (https://wzrd.in/standalone/expect@latest:1:26228)
at testToggleTodo (todadelipu.js:22:34)
    at todadelipu.js:25:1"
```

**Use Object.assign() method**
* [Object.assign(target, …sources) ](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) only copies enumerable and own properties from a source object to a target object 
* In our e.g., the left arg {} is the one whose properties are going to be assigned. Its going to be mutated, so we pass in an empty object as the first arg to avoid mutating any data
* Every further argument will be considered as source objects whose properties will be copied to target object 
* ** IF several sources specify different values for the same property, the last one wins 
* This is what we’re using to override the completed field despite what the original todo object says 
```Javascript
const toggleTodo = (todo) => {
  return Object.assign({}, todo, {
    completed: !todo.completed;
  });
};
```
