# 19 React Redux Todo List - Visibility Filter 

[Lesson Video](https://egghead.io/lessons/javascript-redux-react-todo-list-example-filtering-todos) 
|| Full example [here](https://github.com/huiwenhw/learn-redux/blob/master/19_redux_react_todolist_full.html)

In this lesson we’re gonna use the visibility filter field to let the user choose the todos that he/she wants to see (all, active or completed todos) 

**FilterLink**
* First we create a functional component filter link, that the user has to click to show the diff todos 
* Accepts filter (string) and children (contents of the link) 
* <a> tag prevents navigation on link and is going to dispatch an action with the type to SET_VISIBILITY_FILTER and pass in the filter prop so that the reducer knows which filter is being clicked 
* We then pass the children down to the <a> tag so that the user can specify the text of the link 
```Javascript
const FilterLink = ({filter, currentFilter, children}) => {
  return (
    <a href='#'
      onClick={e => {
        e.preventDefault();
        store.dispatch({
          type: 'SET_VISIBILITY_FILTER',
          filter
        });
      }}>
        {children}
    </a>
  );
};
```

**Adding FilterLink into our Todo Component**
* We add a paragraph <p> to offer the user a choice on which todos should be visible by using the filter link component we just created 
* The filter prop is one of the 3 possible values: SHOW_ALL, SHOW_ACTIVE, SHOW_COMPLETED
* Running this will show the 3 different options below the list of todos 
* Clicking on the link will change the visibility filter field buttt it doesnt work yet 
```Javascript
class TodoApp extends Component {
  render() {
    const {
      todos, 
      visibilityFilter
    } = this.props;
    const visibleTodos = getVisibleTodos(
      todos,
      visibilityFilter
    );
    return (
      ...
        <p>
          Show: 
          {' '}
          <FilterLink 
            filter='SHOW_ALL'
          > 
            All 
          </FilterLink>
          {' '}
          <FilterLink 
            filter='SHOW_ACTIVE'
          >
            Active
          </FilterLink>
          {' '}
          <FilterLink 
            filter='SHOW_COMPLETED'
          >
            Completed
          </FilterLink>
         </p>
      </div>
    );
  }
}
```

**Create function to filter todos**
* We then need to create a new func to help filter our todos 
* It accepts the todos array and the filter 
* If filter is SHOW_ALL, we’re just gonna return all of the todos
* if filter is SHOW_COMPLETED, we’re gonna filter the todos array and return the ones that have completed set to true
* if filter is SHOW_ACTIVE, we’re gonna filter the todos array & return the ones that have completed set to false 
```Javascript
const getVisibleTodos = (todos, filter) => {
  switch(filter) {
    case 'SHOW_ALL':
      return todos;
    case 'SHOW_COMPLETED':
      return todos.filter(
        t => t.completed
      );
    case 'SHOW_ACTIVE':
      return todos.filter(
        t => !t.completed
      );
  }
};
```

**Now, we need to call this function to filter the todos before rendering them**
* We can destructure the todos and visibly filter from the props so that we can access them directly without typing this.props every time 
* In the render func of the TodoApp component, we get the visible todos by calling getVisibleTodos with the todos and the visibility filter values from the props
* and now we’re gonna use this visibleTodos instead of this.props.todos when we enumerate them for rendering 
```Javascript
class TodoApp extends Component {
  render() {
    const {
      todos, 
      visibilityFilter
    } = this.props;
    const visibleTodos = getVisibleTodos(
      todos,
      visibilityFilter
    );
    return (
      <div>
        ...
        <ul>
          {visibleTodos.map(todo => 
             <li key={todo.id}
                 ...
             </li>
           )}
        </ul>
      </div>
    );
  }
}
```

* Finally, we now use the visibility filter inside our TodoApp component, so we need to pass it as a prop
* We could do this explicitly, but its easier for us to just spread over all the state fields so every state field inside the state object is passed as a prop 
* This way, it receives the visibility filter 
```Javascript
const render = () => {
  ReactDOM.render(
    <TodoApp {...store.getState()} />,
    document.getElementById('root')
  );
};
```

**But, we want to highlight the active filter**
* To do this, we need the visibility filter prop which says which is the current one 
* We can then apply different styling if the current filter matches the filter link’s own filter 
```Javascript
class TodoApp extends Component {
  render() {
    ...
    return (
      ...
        <p>
          Show: 
          {' '}
          <FilterLink 
            filter='SHOW_ALL'
            currentFilter={visibilityFilter}
          > 
            All 
          </FilterLink>
          {' '}
          <FilterLink 
            filter='SHOW_ACTIVE'
            currentFilter={visibilityFilter}
          >
            Active
          </FilterLink>
          {' '}
          <FilterLink 
            filter='SHOW_COMPLETED'
            currentFilter={visibilityFilter}
          >
            Completed
          </FilterLink>
         </p>
      </div>
    );
  }
}
```

* We then go back to the FilterLink declaration 
* We first add currentFilter as a prop, and a condition that says 
* when the filter is the current filter, we want to return a span instead of a link cause we dont want it to be clickable. We want static text 
```Javascript
const FilterLink = ({filter, currentFilter, children}) => {
  if(filter === currentFilter) {
    return <span>{children}</span>
  }
  return (
    <a href='#'
      onClick={e => {
        e.preventDefault();
        store.dispatch({
          type: 'SET_VISIBILITY_FILTER',
          filter
        });
      }}>
        {children}
    </a>
  );
};
```