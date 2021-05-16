# "This" Error

## This in javascript

In JavaScript, functions are objects, and the value of "this" depends on how a function is called.

Here's two exemples

```js
class Square {
  constructor(size) {
    this.size = size;
  }
  computePerimeter() {
    return this.size * 4;
  }
}

const square = new Square(6);
const computePerimeter = square.computePerimeter;
```

**Result**

```js
expect(computePerimeter).toEqual("Cannot read property 'size' of undefined");
```

Same example with arrow function.
In arrow functions, "this" corresponds to the value of the enclosing context.

```js
class Square {
  constructor(size) {
    this.size = size;
    this.computePerimeter = () => this.size * 4;
  }
}

const square = new Square(6);
const computePerimeter = square.computePerimeter;
```

**Result**

```js
expect(computePerimeter()).toEqual(24);
```

## Avoid "This is undefiend" Error in React

Here's a example of component which will return an error when user click on the button.

❌ Error

```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  handleClick() {
    this.setState((prev) => ({
      count: prev.count + 1,
    }));
  }

  render() {
    return (
      <button type="button" onClick={this.handleClick}>
        Click Me ({this.state.count})
      </button>
    );
  }
}
```

### Solution 1: Bind "this"

✔️ Bind this in the render function

```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  handleClick() {
    this.setState((prev) => ({
      count: prev.count + 1,
    }));
  }

  render() {
    return (
      <button type="button" onClick={this.handleClick.bind(this)}>
        Click Me ({this.state.count})
      </button>
    );
  }
}
```

✔️ Bind this in the constructor

```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState((prev) => ({
      count: prev.count + 1,
    }));
  }

  render() {
    return (
      <button type="button" onClick={this.handleClick}>
        Click Me ({this.state.count})
      </button>
    );
  }
}
```

### Solution 2: Use Arrow function

✔️ Use arrow function in the render method

```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  handleClick() {
    this.setState((prev) => ({
      count: prev.count + 1,
    }));
  }

  render() {
    return (
      <button type="button" onClick={() => this.handleClick()}>
        Click Me ({this.state.count})
      </button>
    );
  }
}
```

✔️ Use arrow function in class property

```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  handleClick = () => {
    this.setState((prev) => ({
      count: prev.count + 1,
    }));
  };

  render() {
    return (
      <button type="button" onClick={this.handleClick}>
        Click Me ({this.state.count})
      </button>
    );
  }
}
```
