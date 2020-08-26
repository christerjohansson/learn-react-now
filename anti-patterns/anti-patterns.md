# Introduction

Familiarizing ourselves with common anti-patterns will help us understand how React works and describe useful forms of refactoring our code.

## What is anti-patterns?

[Anti-patterns](http://en.wikipedia.org/wiki/Anti-pattern) are certain patterns in software development that are considered bad programming practices. As opposed to design patterns which are common approaches to common problems which have been formalized and are generally considered a good development practice, anti-patterns are the opposite and are undesirable.

## Use of index as key in rendering with loops

While rendering a list of similar components, keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements inside the array to give the elements a stable identity. And most JS developers tend to use array index as key which can be considered as an anti-pattern.

Here is an official documentation from [https://reactjs.org/](https://reactjs.org/):

> We don’t recommend using indexes for keys if the order of items may change. This can negatively impact performance and may cause issues with component state. When you don’t have stable IDs for rendered items, you may use the item index as a key as a last resort.

Here's a blog explaining why an index as key is bad practice: [Why using an index as Key in React is probably a bad idea?](https://medium.com/@vraa/why-using-an-index-as-key-in-react-is-probably-a-bad-idea-7543de68b17c)

### Solution

Keys should be stable, unique & predictable. Index, random numbers or timestamps.

```javascript
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```

## Constant placement in components

When defining constants, we have to decide the placement in the component. Most developers tend to define constant object literals and arrays inside the render methods in react. This approach might affect the performance and the lag can be visible in large components.

```javascript
class App extends Component {
  render() {
    const styles = { width: "400px", height: "400px", position:  "relative" };
}
}
```

### Solution

When you want do define some constants for images & styles, it is advised to define outside the component.

```javascript
const styles = { width: "400px", height: "400px", position: "relative" };
class MyComponent extends React.component {}
```

We also can define the constants inside constructor.

```javascript
class MyComponent extends React.component {
  constructor(){
    this.styles = { width: "400px", height: "400px", position:
  }
}
```

## Managing derived state from props

In class based components, _componentWillReceiveProps_ \(to be deprecated\) & _getDerivedStateFromProps_ offer a way to update state in response to the change in props.

### According to React docs:

> A common misconception is that _getDerivedStateFromProps_ and _componentWillReceiveProps_ are only called when props “change”. These lifecycles are called any time a parent component re-renders, regardless of whether the props are “different” from before. Because of this, it has always been unsafe to unconditionally override state using either of these lifecycles. **Doing so will cause state updates to be lost.**

```javascript
class EmailInput extends Component {
  state = { email: this.props.email };

  render() {
    return <input onChange={this.handleChange} value={this.state.email} />;
  }

  handleChange = event => {
    this.setState({ email: event.target.value });
  };

  componentWillReceiveProps(nextProps) {
    // This will erase any local state updates!
    // Do not do this.
    this.setState({ email: nextProps.email });
  }
}
```

In above example, state is updated based on props received. But if the component’s parent re-renders, any update to the state will be lost.

### Solution

* State management libraries like redux maintaining single source of truth.
* Data down action up approach can be used which emphasizes unidirectional data flow. This approach prioritizes passing down data from one part of application down its children components. The children component has readonly access to the data but it cannot modify the data directly. Instead, the children component can have access to actions or callbacks which can be used to update the data in parent component.

## Common promise anti-pattern

Developers working with `promises` and `async await` in JavaScript can make common mistakes which can introduce confusing blogs in an application.

```javascript
loadSomething().then(function(something) {
    loadAnotherthing().then(function(another) {
                    DoSomethingOnThem(something, another);
    });
});
```

### Solution

This makes it difficult to refactor the codebase and nesting of promises can introduce bugs if not handled correctly. A cleaner way could be use of `promise.all`

```javascript
promise.all([loadSomething(), loadAnotherThing()])
 .spread(function(something, another) {
 doSomethingOnThem(something, another);
});
```

In case you are using `async await`

```javascript
 const async myFunction(){
  const res1 = await dataFromApi()
  const res2 = await dataFromAnotherApi()  
  doSomething(res1) //blocked by dataFromAnotherApi which is wrong
}
```

`async await` in a function blocks the execution of the code until the response is resolved. So, if the variable does not even depend on the promise to be resolved, it can be considered as an anti-pattern as in above example.

## Using setState

The setState is pretty simple & straight forward concept in React. But developers might over look some of the nuisances when using `setState`

Consider the following:

```javascript
class MyComponent extends Component {
  constructor(props) {
    super(props);
    this.state = {
      counter: 350
    };
  }
updateCounter() {
    // this line will not work
    this.state.counter = this.state.counter + this.props.increment;

    // this will not work as intended
    this.setState({
      counter: this.state.counter + this.props.increment; 
    });

  }
  ...
}
```

The `setState` method is asynchronous. Hence, we should not use the use the value state itself inside `setState` as above.

We can use the following syntax to avoid the issue:

```javascript
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

`prevState` is a name that given to the argument passed to `setState` callback function. It holds is the value of state before the `setState` was triggered by React.

## async functions in useEffect

It is necessary that we use async api calls using `useEffect` hook. So, we might be inclined to write something like this:

```javascript
useEffect(async () => {
    try {
        const response = await fetch(http://hn.algolia.com/api/v1');
        setData(response)
    } catch (e) {
        console.error(e);
    }
}, []);
```

According to [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction), The async function declaration defines an asynchronous function, which returns an AsyncFunction object. However, an effect hook must return nothing or a clean up function. We then get a warning about `useEffect` must return a cleanup function or nothing. The correct approach would be as below:

```javascript
useEffect(() => {
   const fetchData = async () => {
   const result = await axios('https://hn.algolia.com/api/v1');
   setData(result.data);
};
  fetchData();
}, []);
```

## Multiple useState hooks

```javascript
const [user, setUser] = useState('');
const [loading, setLoading] = useState(true);
const [error, setError] = useState('');
```

We should always use multiple `useState` hooks instead of one big one like in `setState` because the react hooks has changed how react internally handles updates to objects within the state. In class based components, calling `setState` would merge new values into the existing state object.

If there were keys whose values had not changes, there would not be any need to replace the existing values at those keys. However, `useState` hook entirely replaces the underlying value. If we had an object with all state properties, we would be replacing the entire state on every update with `useState` hooks. So, we need to split of states into multiple hooks so that the react engine replaces the states in isolation.

