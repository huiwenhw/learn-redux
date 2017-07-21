# 15/16 Reducer composition with combineReducers() & implementing combineReducers() from scratch

[16 Lesson Video](https://egghead.io/lessons/javascript-redux-reducer-composition-with-combinereducers) || [15 Lesson Video](https://egghead.io/lessons/javascript-redux-implementing-combinereducers-from-scratch)
Full example [here](https://github.com/huiwenhw/learn-redux/blob/master/15_16_combineReducers.html)

* In previous lesson, we used the reducer composition pattern to let diff reducers handle diff parts of the state tree & combine their results 
* Redux provides a func called combineReducers that generates this top level reducer
* The only argument to combineReducers is an object
* This object lets us specify the mappings between the state field names & reducers managing them 
* Keys of the object == fields of the state object 
* Values of the object == reducers it should call to update the corresponding state fields 
* This combineReducers call says that the todos field inside the state object manages will be updated by the todos reducer
* and the visibilityFilter field inside the state object will be updated by calling the visibilityFilter reducer 
* Result will be assembled into a single object 
```Javascript
const { combineReducers } = Redux;
const todoApp = combineReducers({
  todos: todos,
  visibilityFilter: visibilityFilter
});
```

* behaves similar to the function we wrote previously 
```Javascript
const todoApp = (state = {}, action) => {
   return {
     todos: todos(
       state.todos, 
       action
     ),
     visibilityFilter: visibilityFilter(
       state.visibilityFilter, 
       action
     )
   };
};
```

* Good convention: Name our reducers after the state keys they manage
* Since key & value names are the same, we can omit the values due to the ES6 object literal shorthand notation  
```Javascript
const { combineReducers } = Redux;
const todoApp = combineReducers({
  todos,
  visibilityFilter
});
```

**Implementing combineReducers() from scratch**
* combineReducers is a func, so we’re writing a func declaration
* Its argument is the mapping btwn state keys and the reducers 
* The return value is supposed to be a reducer itself, so this is a func that returns another func 

* The signature of the return function is a reducer signature. It has the state & action 
* Next, we’re calling Object.keys method, which gives all the keys of the reducer’s object -> in this case, its the todos and visibility filter 
* We then call the reduce method on the keys as we want to produce a single value, such as the next state, by accumulating over every reducer key and calling the corresponding reducer 

* Each reducer passed through the combineReducers function is only responsible for updating a part of the state 
* The next state by the given key can be calculated by calling the corresponding reducer by the given key with the current state by the given key and the action.
* i.e. nextState is the accumulator, key is the current element being processed [link](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
* reducers[key] (state[key], action) === todos(state.todos, action) 
* the array reduce wants us to return the next accumulated value from the callback, so we’re returning the nextState 
* We specify an empty object as an initial next state, before the keys are processed 
```Javascript
const combineReducers = (reducers) => {
  return (state = {}, action) => {
     return Object.keys(reducers).reduce(
        (nextState, key) => {
          nextState[key] = reducers[key](
            state[key], 
            action
          );
          return nextState;
        }, {}
     );
  };
};

const todoApp = combineReducers({
  todos,
  visibilityFilter
});
```