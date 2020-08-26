# Anti-patterns

Familiarizing ourselves with common anti-patterns will help us understand how React works and describe useful forms of refactoring our code.

## What is anti-patterns?
[Anti-patterns](http://en.wikipedia.org/wiki/Anti-pattern) are certain patterns in software development that are considered bad programming practices.
As opposed to design patterns which are common approaches to common problems which have been formalized and are generally considered a good development practice, anti-patterns are the opposite and are undesirable.


## Use of index as key in rendering with loops

While rendering a list of similar components, keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements inside the array to give the elements a stable identity. And most JS developers tend to use array index as key which can be considered as an anti-pattern.

Here is an official documentation from https://reactjs.org/:

> We don’t recommend using indexes for keys if the order of items may change. This can 
negatively impact performance and may cause issues with component state. When you don’t have stable IDs for rendered items, you may use the item index as a key as a last resort.

Here's a blog explaining why an index as key is bad practice:
[Why using an index as Key in React is probably a bad idea?](https://medium.com/@vraa/why-using-an-index-as-key-in-react-is-probably-a-bad-idea-7543de68b17c)

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

In class based components, *componentWillReceiveProps* (to be deprecated) & *getDerivedStateFromProps* offer a way to update state in response to the change in props.

### According to React docs:

> A common misconception is that *getDerivedStateFromProps* and *componentWillReceiveProps* are only called when props “change”. These lifecycles are called any time a parent component re-renders, regardless of whether the props are “different” from before. Because of this, it has always been unsafe to unconditionally override state using either of these lifecycles. **Doing so will cause state updates to be lost.**

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

- State management libraries like redux maintaining single source of truth.

- Data down action up approach can be used which emphasizes unidirectional data flow. This approach prioritizes passing down data from one part of application down its children components. The children component has readonly access to the data but it cannot modify the data directly. Instead, the children component can have access to actions or callbacks which can be used to update the data in parent component.

