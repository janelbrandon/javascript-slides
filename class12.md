---
theme : "sky"
highlightTheme : "agate"
---

<style>
    .reveal .slides {
      zoom: 1 !important;
      height: auto !important;
    }
    .reveal .slides section {
      top: 50% !important;
      transform: translateY(-50%) !important;
      zoom: 0.75 !important;
    }
    .reveal pre {
        width: 100% !important;
    }
    .reveal section pre code {
        overflow: hidden !important;
        max-height: none !important;
        white-space: pre-wrap !important;
    }
    .reveal img {
        border: none !important;
        background: none !important;
    }
</style>

# React State

---

## State and Lifecycle

Consider the following ticking clock example. One issue with it is that the tick() function has to call render() every second so we can see the changes in the DOM.

```
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  )
  ReactDOM.render(
    element,
    document.getElementById('root')
  )
}

setInterval(tick, 1000)
```

---

## Clock Component

To solve this problem, let's create a reusable `Clock` component, which will set up its own timer and update itself every second.

We can start by encapsulating how the clock looks:

```
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  )
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  )
}

setInterval(tick, 1000)
```

---

## Encapsulation

However, it misses a crucial requirement: the fact that the `Clock` sets up a timer and updates the UI every second should be an implementation detail of the `Clock` component, rather than outside of it. That way we _encapsulate_ the functionality, making `Clock` a self-contained component.

Ideally we want to write the following _once_ and have the `Clock` update itself:

```
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)
```

To implement this, we need to add _state_ to the `Clock` component.

---

## State

State is similar to props, but it is private and fully controlled by the component. Also, state is mutable, whereas props are immutable (read-only).

So far we've been declaring components as functions (functional components), but we can also declare them using ES6 classes.

Components defined as classes have some additional features. Local state is exactly that: a feature available only to classes.

If we know that a given component we're building will never need internal state (a stateless component), then the convention is to write it as a functional component.

---

## Convert Function to Class

Let's convert our functional component to a class.

1.  Create an [ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes), with the same name, that extends `React.Component`.
    
2.  Add a single empty method to it called `render()`.
    
3.  Move the body of the function into the `render()` method.
    
4.  Replace `props` with `this.props` in the `render()` body.
    
5.  Delete the remaining empty function declaration.
    

```
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```

---

## Class component

```
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```

`Clock` is now defined as a class rather than a function.

The `render` method will be called each time an update happens, but as long as we render `<Clock />` into the same DOM node, only a single instance of the `Clock` class will be used. This lets us use additional features such as local state and lifecycle methods.

---

## Add Local State to a Class

We will move the `date` from props to state in three steps:

`1.` Replace `this.props.date` with `this.state.date` in the `render()` method:

```js
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```

---

## Add Local State to a Class

`2.` Add a [class constructor](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor) that assigns the initial `this.state`:

```js
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```

Note how we pass `props` to the base constructor:

```js
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }
```

Class components should always call the base constructor with `props`. This ensures that the React internals of the component are correctly initialized.

---

## Add Local State to a Class

`3.` Remove the `date` prop from the `<Clock />` element:

```js
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)
```

We will later add the timer code back to the component itself.

---

## Finished class component

The end result looks like this:

```js
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)
```

Next, we'll make the `Clock` set up its own timer and update itself every second.

---

## Lifecycle Methods

In applications with many components, it's very important to free up resources taken by the components when they are destroyed.

We want to [set up a timer](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setInterval) whenever the `Clock` is rendered to the DOM for the first time. This is called _mounting_ in React.

We also want to [clear that timer](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/clearInterval) whenever the DOM produced by the `Clock` is removed. This is called _unmounting_ in React.

We can declare special methods on the component class to run some code when a component mounts and unmounts.

These methods are called _lifecycle methods_.

---

## Lifecycle Methods

```js
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```

---

## Lifecycle Methods

The `componentDidMount()` method runs after the component output has been rendered to the DOM. This is a good place to set up a timer:

```js
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    )
  }
```

Note how we save the timer ID right on `this`. We don't need to store it in `this.state` because the `timerID` will never change once set, and has nothing to do with the DOM.

If we wanted to show the timerID in the DOM tree somewhere, then we'd need to put it in `this.state`

---

## Lifecycle Methods

We will tear down the timer in the `componentWillUnmount()` lifecycle method:

```js
  componentWillUnmount() {
    clearInterval(this.timerID)
  }
```

clearInterval stops the timer with the given ID and destroys it.

---

## Lifecycle Methods

Finally, we will implement a method called `tick()` that the `Clock` component will run every second. It will use `this.setState()` to schedule updates to the component local state:

```js
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    )
  }
  componentWillUnmount() {
    clearInterval(this.timerID)
  }
  tick() {
    this.setState({
      date: new Date()
    })
  }
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
)
```

---

## Data flow recap

Now the clock ticks every second. Let’s quickly recap what’s going on and the order in which the methods are called:

1. When `<Clock />` is passed to `ReactDOM.render()`, React calls the constructor of the `Clock` component. Since `Clock` needs to display the current time, it initializes `this.state` with an object including the current time. We will later update this state.
    
2. React then calls the `Clock` component’s `render()` method. This is how React learns what should be displayed on the screen. React then updates the DOM to match the `Clock`’s render output.
    
3. When the `Clock` output is inserted in the DOM, React calls the `componentDidMount()` lifecycle method. Inside it, the `Clock` component asks the browser to set up a timer to call the component’s `tick()` method once a second.

---

## Data flow recap

4. Every second the browser calls the `tick()` method. Inside it, the `Clock` component schedules a UI update by calling `setState()` with an object containing the current time.

5. Thanks to the `setState()` call, React knows the state has changed, and calls the `render()` method again to learn what should be on the screen. This time, `this.state.date` in the `render()` method will be different, and so the render output will include the updated time.

6. React updates the DOM with the new data.
    

If the `Clock` component is ever removed from the DOM, React calls the `componentWillUnmount()` lifecycle method so the timer is stopped.

---

## Using State Correctly

There are three things you should know about `setState()`.

### Do Not Modify State Directly

For example, this will not re-render a component:

```js
// Wrong
this.state.comment = 'Hello'
```

Instead, use `setState()`, which will change the state, then notify React that the state has changed:

```js
// Correct
this.setState({comment: 'Hello'})
```

The only place where you can assign `this.state` is the constructor.

---

### State Updates May Be Asynchronous

React may batch multiple `setState()` calls into a single update for performance.

Because `this.props` and `this.state` may be updated asynchronously, you should not rely on their values for calculating the next state.

For example, this code may fail to update the counter:

```js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
})
```

To fix it, use a second form of `setState()` that accepts a function rather than an object. That function will receive the previous state as the first argument, and the props at the time the update is applied as the second argument:

```js
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}))
```

---

### State Updates are Merged

When you call `setState()`, React merges the object you provide into the current state. For example, your state may contain several independent variables:

```js
  constructor(props) {
    super(props)
    this.state = {
      posts: [],
      comments: []
    }
  }
```

Then you can update them independently with separate `setState()` calls:

```js
  componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      })
    })
    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      })
    })
  }
```

The merging is shallow, so `this.setState({comments})` leaves `this.state.posts` intact, but completely replaces `this.state.comments`.

---

## The Data Flows Down

Neither parent nor child components can know if a certain component is stateful or stateless, and they shouldn't care whether it is defined as a function or a class.

This is why state is often called local or encapsulated. It is not accessible to any component other than the one that owns and sets it.

---

## The Data Flows Down

A component may choose to pass its state down as props to its child components:

```xml
<FormattedDate date={this.state.date} />
```

In the above example, the parent of this `FormattedDate` component is passing down a property from it's own state (in this case, date).

The `FormattedDate` component would receive the `date` in its props, but it doesn't (and shouldn't) know whether it came from the `Clock`’s state, from the `Clock`’s props, a variable or constant, a hard-coded value, etc...

---

## The Data Flows Down

```js
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>
}
```

This is commonly called a _top-down_ or _unidirectional_ data flow. Any state is always owned by some specific component, and any data or UI derived from that state can only affect components _below_ them in the tree.

This way, child components are more re-usable, since they don't depend on a particular parent component in order to work.

---

## Events

Handling events with React elements is very similar to handling events on DOM elements. There are some syntactic differences:

>* React events are named using camelCase, rather than lowercase.
* With JSX you pass a function as the event handler, rather than a string.

For example, the HTML:

```xml
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

is slightly different in React:

```xml
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

---

## Events

Here we're setting an onClick event on the button within a class-based component. When clicked, we console log the object and also change the button caption.

```js
class LoggingButton extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      caption: "Click Me!"
    }
  }

  handleClick = () => {
    console.log('this is:', this)
    this.setState({caption: 'Do not click this button'})
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.caption}
      </button>
    )
  }
}
```

Note that we didn't change the button caption in the DOM, only in the state. React updates the DOM automatically when we change the state. Indeed, we should _never_ change the DOM directly in a React app.

---

# Questions?

---

## Resources

[React Docs - State and Lifecycle](https://reactjs.org/docs/state-and-lifecycle.html)

[React Docs - Handling Events](https://reactjs.org/docs/handling-events.html)

---

## AFTERNOON CHALLENGE

### [Canvas Unit: React State](https://coderacademy.instructure.com/courses/144/pages/unit-react-state)

