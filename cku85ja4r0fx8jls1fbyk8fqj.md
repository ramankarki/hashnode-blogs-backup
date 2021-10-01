## Multiple ways of using React useState hook in our projects

While I was learning React and building projects, I found multiple ways of using React `useState` hook in our projects.

So I wanted to share with my fellow developers and myself in future cause I will be working on multiple libraries, So I may forget these things 😂😂

## What is React hook?
Hooks are a new addition in React 16.8. They let you use state and other React features without writing a class.

### Example: 

Just like we used states in our **Class Based Components**,
```jsx
import React from 'react';

class Test extends React.Component {
  constructor(props) {
    super(props);
    this.state = { test: 'This is state' };
  }

  onClick = () => this.setState({ test: 'State changed on click' });

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <button onClick={this.onClick}>{this.state.test}</button>
      </div>
    );
  }
}

export default Test;
```

We initialize states in **Functional components** like this,

```
import { useState } from 'react';

function Test() {
  const [state, setState] = useState('This is state');

  const onClick = () => setState('State changed on click');

  return (
    <div>
      <h1>Hello, world!</h1>
      <button onClick={onClick}>{state}</button>
    </div>
  );
}

export default Test;
```

Just so you know, We can initialize multiples states with `useState`,

```js
function Test() {
  const [state, setState] = useState('This is state');
  const [state1, setState1] = useState('This is another state');
  const [state2, setState2] = useState('And another state');

  const onClick = () => setState('State changed on click');

  return (
    <div>
      <h1>Hello, world!</h1>
      <button onClick={onClick}>{state}</button>
    </div>
  );
}
```
This was pretty simple and straight forward way of using `useState` React hook.

---

### 💡 Some important points to keep in mind:
- We never update states directly.
```js
state = 'whatever'
```
This won't re-render our component. Hence, it won't be reflected in our browser.

- State updates are asynchronous. So we can't update state in some like of loops, conditions, or nested functions. React development server will through an error.
<div>
```
Uncaught (in promise) Invariant Violation: Maximum update depth exceeded. This can happen when a component repeatedly calls setState inside componentWillUpdate or componentDidUpdate. React limits the number of nested updates to prevent infinite loops.
```
</div>

- Only call Hooks from React functional components or our own custom Hooks.

- React hooks doesn't work inside **Class Based Components**.

🥳🥳 *Let's see some other ways of using `useState` hook in our projects.*

## Passing callback function inside `setState` function

Let me explain how we can use callback function inside `setState` function. Let's build a simple todo list that looks something like this,

![todo list.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1633072365877/OP5bvYfut.png)

*There is no styling, just plane html syntax.*

```javascript
import { useState } from 'react';

function Test() {
  const [list, setList] = useState([]);
  const [input, setInput] = useState('');

  const onSubmit = (event) => {
    event.preventDefault();
    setList((prev) => [...prev, input]);
    setInput('');
  };

  const onChange = (event) => setInput(event.target.value);

  const onDelete = (index) => (event) =>
    setList((prev) => [...prev.slice(0, index), ...prev.slice(index + 1)]);

  return (
    <div>
      <h1>Hello, world!</h1>
      <ul>
        {list.map((list, index) => (
          <li key={list + index}>
            {list} <button onClick={onDelete(index)}>x</button>
          </li>
        ))}
      </ul>
      <form onSubmit={onSubmit}>
        <input type="text" value={input} onChange={onChange} />
        <button>Add</button>
      </form>
    </div>
  );
}

export default Test;
```

*We can initialize state value with any valid datatype.*

We usually pass a value and that value becomes the new state. But `setState` function also takes a callback function that receives previous state as an argument and whatever the function returns will the next value for that state.

## Passing `setState` function as a props for child component
This technique is mainly useful for lifting child state in its parent component.

```javascript
// parent.jsx
import { useState } from 'react';
import Child from './child';

function parent() {
  const [parent, setParent] = useState('hello');
  return (
    <>
      <h1>{parent}</h1>
      <Child setParent={setParent} />
    </>
  );
}

export default parent;
```

```
// child.jsx
import { useState, useEffect } from 'react';

function child(props) {
  const [child, setChild] = useState('whatever');

  useEffect(() => {
    props.setParent(child);
  }, []);

  return <h3>{child}</h3>;
}

export default child;
```

If you try to run these files, Both `h1` and `h3` will render 'whatever'. The reason is, initially the parent state value is 'hello', But when child component gets mounted,

```javascript
  useEffect(() => {
    props.setParent(child);
  }, []);
```

this code gets executed and `setParent` state function which is passed as a props in its child component, sets the parent state value same as the child state value. Therefore, both the child and parent component renders the same value as 'whatever'.

## Conclusion 
`useState` hook is one of the most important hook for functional component based projects. There are many more ways of using `useState` hook in our projects. This is just the beginning. I have just covered the important use cases. So, that's it on this topic. Let me know if I missed something.

Thank you for reading this blog. ❤️❤️