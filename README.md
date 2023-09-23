# React cheatsheet

Applicable to react 18

## State mgmt
keep it simple and clean.

```js
import { useState } from 'react'

function SomeComponent() {
  const [user, setUser] = useState({
    userName: '',
    userId: ''
  })

  const [msg, setMsg] = useState('')
}
```
### setState

```js
const someHandler = (e) => {
  setUser({
    ...user,
    userName: e.target.value
  });

  setMsg(e.target.value)
}
```
updater function setState (when depends on previous state)
```js
setUser((prevState) => ({
  ...prevState,
  userSalary: userName.length * 500
}));

setMsg(msg => `${msg}!`)
setMsg(msg => `${msg}?`)
setMsg(msg => `${msg}...`)
// msg!?...
```
### context API
to share state between components.
1. Create context in parent
2. Wrap children with `<UserContext.Provider value={user}>`
3. `useState` works in parent component

```js
import { createContext, useContext } from 'react'

const UserContext = createContext({
  userName: '',
  userId: ''
})

function SomeComponent() {
  // Get the state
  const { userName, userId } = useContext(UserContext);
  //...
}

function App() {
  // useState to manage the state
  const [user, setUser] = useState({
    userName: '',
    userId: ''
  });

  return (
    <UserContext.Provider value={user}>
      <SomeComponent />
    </UserContext.Provider>
  )
}

```
Separate dispatch and state contexts and make custom provider for both
```js
// Provider.js
import { createContext, useContext, useReducer } from 'react';

const SomeContext = createContext(null);
const SomeDispatchContext = createContext(null);

export function SomeProvider({ children }) {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    [ { id: 0, text: 'Find Graal', done: true } ] // initial
  );

  return (
    <SomeContext.Provider value={tasks}>
      <SomeDispatchContext.Provider value={dispatch}>
        {children}
      </SomeDispatchContext.Provider>
    </SomeContext.Provider>
  );
}

export function useTasks() {
  return useContext(SomeContext);
}

export function useTasksDispatch() {
  return useContext(SomeDispatchContext);
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    //...
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

// App.js
import AddTask from './AddTask.js'
import TaskList from './TaskList.js'
import { SomeProvider } from './Provider.js'

export default function App() {
  return (
    <SomeProvider>
      <TaskList />
      <AddTask />
    </SomeProvider>
  );
}

//AddTask
import { useState } from 'react'
import { useTasksDispatch, useTasks } from './Provider.js'

export default function AddTask() {
  const [text, setText] = useState('')
  const dispatch = useTasksDispatch()
  const tasks = useTasks()
  return (
    <>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        dispatch({
          type: 'added',
          id: tasks.length + 1,
          text: text
        })
        setText('')
      }}>Add</button>
    </>
  );
}

```
### useReducer
useReducer hook allows to consolidate all the state update logic outside your component in a single function, called a reducer
```js
import { useReducer } from 'react';

const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

function handleAddTask(text) {
  dispatch({
    // "action" object:
    type: 'added', // what happened
    id: nextId++, // other fields
    text: text,
  });
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    //..
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```
### Redux TK
Store + useReducer
```

```
