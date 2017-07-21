# 17 Redux React Todo List 

[Lesson Video](https://egghead.io/lessons/javascript-redux-react-todo-list-example-adding-a-todo)
|| Full example [here](https://github.com/huiwenhw/learn-redux/blob/master/17_18_redux_react_todolist.html)

**Previous**
```Javascript
const todo = (state, action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        id: action.id, 
        text: action.text,
        completed: false
      };
    case 'TOGGLE_TODO':
      if (state.id !== action.id) {
        return state;
      }

      return {
        ...state, 
        completed: !state.completed
      };
    default:
      return state;
  }
}

const todos = (state = [], action) => {
  switch(action.type) {
    case 'ADD_TODO':
      return [
        ...state, // returns all current items
        todo(undefined, action)	// and new item at the end 
      ];
    case 'TOGGLE_TODO':
      return state.map(t => todo(t, action));
    default:
      return state;
  }
};

const visibilityFilter = (
  state = 'SHOW_ALL', 
  action
) => {
  switch(action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter; 
    default:
      return state;
  }
};
```

* Any state change is caused by a store dispatch call somewhere in the component 
* When an action is dispatched, the store calls the reducer it was created with, with the current state & action dispatched
* In our case, its the todoApp reducer, which we obtained by combining todos and visibilityFilter reducer 
```Javascript
const { combineReducers } = Redux;
const todoApp = combineReducers({
  todos,
  visibilityFilter
});

const { createStore } =  Redux;
const store = createStore(todoApp);
```

*Using component from React object*
* React provides a base class for all components 
```Javascript
const { Component } = React; 
```

* We declare a TodoApp component that extends the react-based component 
* This comp is going to have a render function and will return a div 
* We need a sequential ID for every todo
```Javascript
let nextTodoId = 0;
```

* We want to have a button that adds a new todo every time we click it 
* So we dispatch a ADD_TODO action & increment our ID counter 
* We then want to show a list of our current todos
* We do this by calling map for every todo item to map it to a list item 
```Javascript
class TodoApp extends Component {
  render() {
    return (
      <div>
        <button onClick={() => {
          store.dispatch({
            type: 'ADD_TODO',
            text: 'Test',
            id: nextTodoId++
          });
        }}> 
          Add Todo
        </button>
        <ul>
          {this.props.todos.map(todo => 
             <li key={todo.id}>
              {todo.text}
             </li>
           )}
        </ul>
      </div>
    );
  }
}
```

* We then need to pass the todos as a prop to our TodoApp 
* The render function reads the current state of the store and passes the todos array that it gets from the current state to the TodoApp comp as a prop 
* The render func is called on every store change, so the todos prop is always up to date 
* Now every time we press the button, we see a new todo item with a ‘Test’ text 
```Javascript
const render = () => {
  ReactDOM.render(
    <TodoApp todos={store.getState().todos} />,
    document.getElementById('root')
  );
};
```
* The component is being rendered in the render function that runs anytime the store state changes and initially 
```Javascript
store.subscribe(render);
render();
```

* Next, we want to include a input field inside our render function 
* We’re using the react callback ref API
* When the ref attribute is used on an HTML element, the ref callback receives the underlying DOM element as its argument [link](https://facebook.github.io/react/docs/refs-and-the-dom.html)
* Here we’re using the ref callback to store a reference to the text input DOM element in an instance field (e.g. this.input) 
```Javascript
class TodoApp extends Component {
  render() {
    return (
      <div>
        <input ref={node => {
           this.input = node;
        }} />
        <button onClick={() => {
          store.dispatch({
            type: 'ADD_TODO',
            text: this.input.value,
            id: nextTodoId++
          });
          this.input.value = '';
        }}> 
          Add Todo
        </button>
        <ul>
          {this.props.todos.map(todo => 
             <li key={todo.id}>
              {todo.text}
             </li>
           )}
        </ul>
      </div>
    );
  }
}
```

# 18 Redux React Todo List - Toggle 

[Lesson Video](https://egghead.io/lessons/javascript-redux-react-todo-list-example-toggling-a-todo)

**This lesson we want to toggle a todo item by clicking on them**
* So we want to dispatch an action to the store with the type TOGGLE_TODO & todo id when they click on a todo item 
* The event handler knows which todo it corresponds to, so its able to pass its ID in the action 
* In the UI, we want to add a style to show a strike through when the todo is completed 
```Javascript
class TodoApp extends Component {
  render() {
    return (
      <div>
        ...
        <ul>
          {this.props.todos.map(todo => 
             <li key={todo.id}
                 onClick={() => {
                   store.dispatch({
                     type: 'TOGGLE_TODO',
                     id: todo.id
                    });
                  }}
                  style={{
                    textDecoration: 
                      todo.completed ? 'line-through' : 'none'
                  }}>
              {todo.text}
             </li>
           )}
        </ul>
      </div>
    );
  }
```