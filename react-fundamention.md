# ReactFundamention
## 1. What is the difference between state and props?
[props](https://reactjs.org/docs/components-and-props.html) (**short for “properties”**) and [state](https://reactjs.org/docs/state-and-lifecycle.html) are both plain JavaScript objects. 
While both hold information that influences the output of render, they are different in one important way: props get passed to the component (**similar to function parameters**) whereas state is managed within the component (**similar to variables declared within a function**).
## 2. What is the difference between passing an object or a function in setState?
Passing an update function allows you to access the current state value inside the updater. Since `setState' calls are batched, this lets you chain updates and ensure they build on top of each other instead of conflicting:

**Wrong value**
```js
incrementCount() {
  // Note: this will *not* work as intended.
  this.setState({count: this.state.count + 1});
}

handleSomething() {
  // Let's say `this.state.count` starts at 0.
  this.incrementCount();
  this.incrementCount();
  this.incrementCount();
  // When React re-renders the component, `this.state.count` will be 1, but you expected 3.

  // This is because `incrementCount()` function above reads from `this.state.count`,
  // but React doesn't update `this.state.count` until the component is re-rendered.
  // So `incrementCount()` ends up reading `this.state.count` as 0 every time, and sets it to 1.

  // The fix is described below!
}
```
**True value**
```js
incrementCount() {
  this.setState((state) => {
    // Important: read `state` instead of `this.state` when updating.
    return {count: state.count + 1}
  });
}

handleSomething() {
  // Let's say `this.state.count` starts at 0.
  this.incrementCount();
  this.incrementCount();
  this.incrementCount();

  // If you read `this.state.count` now, it would still be 0.
  // But when React re-renders the component, it will be 3.
}
```

## 3. Should I use a state management library like Redux or MobX?
[Maybe.](https://redux.js.org/faq/general#when-should-i-use-redux)

It’s a good idea to get to know React first, before adding in additional libraries. You can build quite complex applications using only React.

## 4. When should I use Redux?
**In general, use Redux when you have reasonable amounts of data changing over time, you need a single source of truth, and you find that approaches like keeping everything in a top-level React component's state are no longer sufficient.**

However, it's also important to understand that using Redux comes with tradeoffs. It's not designed to be the shortest or fastest way to write code. It's intended to help answer the question "When did a certain slice of state change, and where did the data come from?", with predictable behavior. It does so by asking you to follow specific constraints in your application: store your application's state as plain data, describe changes as plain objects, and handle those changes with pure functions that apply updates immutably. This is often the source of complaints about "boilerplate". These constraints require effort on the part of a developer, but also open up a number of additional possibilities (such as store persistence and synchronization).

In the end, Redux is just a tool. It's a great tool, and there are some great reasons to use it, but there are also reasons you might not want to use it. Make informed decisions about your tools, and understand the tradeoffs involved in each decision.

## 5. How do I bind a function to a component instance?
**Bind in Constructor (ES2015)**
```js
class Foo extends Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    console.log('Click happened');
  }
  render() {
    return <button onClick={this.handleClick}>Click Me</button>;
  }
}
```
**Class Properties (Stage 3 Proposal)**
```js
class Foo extends Component {
  // Note: this syntax is experimental and not standardized yet.
  handleClick = () => {
    console.log('Click happened');
  }
  render() {
    return <button onClick={this.handleClick}>Click Me</button>;
  }
}
```
**Bind in Render**
```js
class Foo extends Component {
  handleClick() {
    console.log('Click happened');
  }
  render() {
    return <button onClick={this.handleClick.bind(this)}>Click Me</button>;
  }
}
```
>Using an arrow function in render creates a new function each time the component renders, which may have performance implications (see below).

## 6. Why is binding necessary at all?
Relate to object and this in javascript. [Read more](https://hackernoon.com/javascript-es6-arrow-functions-and-lexical-this-f2a3e2a5e8c4)
## 7. How do I pass a parameter to an event handler or callback?
```js
<button onClick={() => this.handleClick(id)} />
```
This is equivalent to calling `.bind`
```js
<button onClick={this.handleClick.bind(this, id)} />
```
**Example: Passing params using data-attributes**
```js
const A = 65 // ASCII character code

class Alphabet extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
    this.state = {
      justClicked: null,
      letters: Array.from({length: 26}, (_, i) => String.fromCharCode(A + i))
    };
  }

  handleClick(e) {
    this.setState({
      justClicked: e.target.dataset.letter
    });
  }

  render() {
    return (
      <div>
        Just clicked: {this.state.justClicked}
        <ul>
          {this.state.letters.map(letter =>
            <li key={letter} data-letter={letter} onClick={this.handleClick}>
              {letter}
            </li>
          )}
        </ul>
      </div>
    )
  }
}
```
