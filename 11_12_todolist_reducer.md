# 11/12 Writing a Todo List Reducer (Adding, toggling a todo) 

[Lesson Video 1](https://egghead.io/lessons/javascript-redux-writing-a-todo-list-reducer-adding-a-todo) 
|| [Lesson Video 2](https://egghead.io/lessons/javascript-redux-writing-a-todo-list-reducer-toggling-a-todo)
|| Full example [here](https://github.com/huiwenhw/learn-redux/blob/master/11_12_todolist_reducer.html)

We want to create a todo list reducer function that handles two actions, adding a new todo and toggling an existing todo. 

**Recap**
* Reducer: Pure function we write to update logic of our app - how the next state is calculated given current state & action being dispatched 
* State = In this example, its an array of todos 

**Adding a todo**
* We want to add a todo 
* The todos reducer function takes in a state array and the action object and returns a new array. it looks at the action type and matches the switch case. 
* The todos reducer then returns items from the old array indicated by …state and the new item representing the added todo 
* The state we passed was an empty array, so we’re gonna get a single item, which is the new todo object 
* We want to make sure that the reducer is a pure function, so we call deep freeze on both state and action 
* We then use expect to ensure that the reducer works as intended 
```Javascript
const todos = (state = [], action) => {
  switch(action.type) {
    case 'ADD_TODO':
       return [
         ...state, 
         {
           id: action.id, 
           text: action.text,
           completed: false
         }
       ];
    default:
       return state;
  }
};

const testAddToDo = () => {
  const stateBefore = [];
  const action = {
    type: 'ADD_TODO', 
    id: 0,
    text: 'Learn Redux'
  };
  const stateAfter = [
    {
      id: 0, 
      text: 'Learn Redux', 
      completed: false
    }
  ];
  
  deepFreeze(stateBefore);
  deepFreeze(action);
  
  expect(
    todos(stateBefore, action)
  ).toEqual(stateAfter);
};

testAddToDo();
console.log('All tests passed!');
```

**Toggling a todo**
* We want to toggle a todo’s completed field 
* We shouldn’t change the original array, so we’re using state.map()  to help produce a new array
* The function we pass as an argument to state.map() will be called for every todo 
* If its not the todo we want to change, we simply return it
* else, we return a new object that has all the properties of the original todo object using the spread operator (…todo) and the inverted value of the completed field 
```Javascript 
const todos = (state = [], action) => {
  switch(action.type) {
    case 'ADD_TODO':
       return [
         ...state, 
         {
           id: action.id, 
           text: action.text,
           completed: false
         }
       ];
    case 'TOGGLE_TODO':
      return state.map(todo => {
        if (todo.id !== action.id) {
          return todo;
        }
        
        return {
          ...todo, 
          completed: !todo.completed
        };
      });
    default:
      return state;
  }
};
```

* We’re testing a different action this time 
* The action is an object with the type property which is a TOGGLE_TODO string the ID of the todo that we want to toggle 
* We’re using deepFreeze on both stateBefore and action as we want to ensure that our reducer function is a pure function 
* We then use expect to assert that the result of calling reducer with stateBefore is equal to stateAfter 

```Javascript
const testToggleTodo = () => {
  const stateBefore = [
    {
      id: 0, 
      text: 'Learn Redux',
      completed: false
    },
    {
      id: 1, 
      text: 'Go Shopping', 
      completed: false
    }
  ];
  const action = {
    type: 'TOGGLE_TODO',
    id: 1
  };
  const stateAfter= [
    {
      id: 0, 
      text: 'Learn Redux',
      completed: false
    },
    {
      id: 1, 
      text: 'Go Shopping', 
      completed: true
    }
  ];
  
  deepFreeze(stateBefore);
  deepFreeze(action);
  
  expect(
    todos(stateBefore, action)
  ).toEqual(stateAfter);
};

testToggleTodo();
console.log('All tests passed!');
```