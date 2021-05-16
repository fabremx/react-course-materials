# Hook "useEffect"

## What it is ?

Hook to allow execution of side effects of your component.
Lifecycle equivalent in class:

- componentDidMount
- componentDidUpdate
- componentWillUnmount

## Equivalent with Class

Examples taking from React docs of `useEffect` equivalent in class

Class component

```js
import React from "react";

class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }
  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

Component using hook

```js
import React, { useState, useEffect } from "react";

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

## Different way to write useEffect

```js
useEffect(() => {
  // Code
});
```

Executed on first render and every update.
Act like:

- componentDidMount
- componentDidUpdate

```js
useEffect(() => {
  // Code
}, []);
```

Executed on first render.
Act like:

- componentDidMount

```js
useEffect(() => {
  // Code
}, [books]);
```

Executed when books change.
Act like:

- componentDidUpdate

## Cleanup

You can handle lifecycle componentWillUnmount with cleanup function.

```js
useEffect(() => {
  // Code

  return () => {
    // Code
  };
});

/* OR */

useEffect(() => {
  // Code

  return () => {
    // Code
  };
}, [books]);
```

⚠️ **Beware**: Cleanup will be trigger every update

Why Cleanup is triggered every update ?

Let’s imagine a component that displays whether a friend is online or not.
We subscribe to the friend status after the component mounts, and unsubscribes during unmounting

```js
 componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
```

When `this.props.friends` changes, we want that the old subscription is removed and a new one is created.

```js
  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate(prevProps) {
    // Unsubscribe from the previous friend.id
    ChatAPI.unsubscribeFromFriendStatus(prevProps.friend.id, this.handleStatusChange);
    // Subscribe to the next friend.id
    ChatAPI.subscribeToFriendStatus(this.props.friend.id, this.handleStatusChange);
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
```

Much easier with a hook to handle it.

```js
function FriendStatus(props) {
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```
