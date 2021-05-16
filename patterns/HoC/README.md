# High Order Component (HoC)

A higher-order component (HoC) is an pattern in React for reusing component logic.
A HoC is a function that accepts a component and returns a new component.

- HoC does not modify or mutate the component. It create new ones.
- HoC is a pure function. That means it has no side effects. It only returns a new component.

## 📘 Context

We will use class component. We will see hooks after.
We create a component which handle a counter when user click on button.

```js
export class ClickCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment() {
    this.setState((prevState) => ({ count: prevState.count + 1 }));
  }

  render() {
    const { count } = this.state;
    return (
      <button onClick={this.increment.bind(this)}>clicked {count} times</button>
    );
  }
}
```

Now we have to create an other component which handle a counter when user hover a text.
Easy ! We copy paste precedent component and adapt it to the new demands.

```js
export class HoverCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment() {
    this.setState((prevState) => ({ count: prevState.count + 1 }));
  }

  render() {
    const { count } = this.state;
    return <p onMouseOver={this.increment.bind(this)}>hovered {count} times</p>;
  }
}
```

We display our component.

```js
export class App extends React.Component {
  render() {
    return (
      <div className="app">
        <ClickCounter />
        <HoverCounter />
      </div>
    );
  }
}
```

## 📌 Problem

Our code is definitely not DRY. Ther counter logic can be extracted and reused.
To do that we will use a HoC.

## 🔑 Solution

Let's create our HoC. The convention say to prefix it by `With`.

```js
export const WithCounter = (WrappedComponent) => {
  class Counter extends React.Component {
    constructor(props) {
      super(props);
      this.state = { count: 0 };
    }

    increment() {
      this.setState((prevState) => ({ count: prevState.count + 1 }));
    }

    render() {
      return (
        <WrappedComponent
          {...this.props}
          count={this.state.count}
          increment={this.increment.bind(this)}
        />
      );
    }
  }

  return Counter;
};
```

Concretlly the component `WithCounter` take a wrapped component and return a new component with the counter logic.
Now let's change our `ClickCounter` and `HoverCounter` components.

```js
class ClickCounter extends React.Component {
  render() {
    const { count, increment } = this.props;
    return <button onClick={increment}>clicked {count} times</button>;
  }
}
export default WithCounter(ClickCounter);

class HoverCoutner extends React.Component {
  render() {
    const { count, increment } = this.props;
    return <p onMouseOver={increment}>hovered {counter} times</p>;
  }
}
export default WithCounter(HoverCoutner);
```

## 📗 HoC VS Hooks

Very often prefer to use custom hooks.

- Does not nedd to require restructuring your components as you use them.
- Making code easier to understand and easier to maintain.

We create our custom hook.

```js
const useCounter = (initalValue) => {
  const [counter, setCounter] = useState(initalValue);

  const increment = () => {
    setCounter((prevState) => prevState + 1);
  };

  return [counter, increment];
};
```

An call it in `ClickCounter` and `HoverCounter` components.

```js
const ClickCounter = () => {
  const [counter, increment] = useCounter(0);
  return <button onClick={increment}>clicked {counter} times</button>;
};
const HoverCounter = () => {
  const [counter, increment] = useCounter(0);
  return <p onMouseOver={increment}>hovered {counter} times</p>;
};
```
