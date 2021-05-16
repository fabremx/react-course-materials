# Refs

Let's see an example of component using Ref.
When user will click on button, input will be focused.

## CLass component

```js
class FocusInputWithButton extends React.Component {
  constructor(props) {
    super(props);
    this.inputEl = React.createRef();
  }

  onButtonClick() {
    this.inputEl.current.focus();
  }

  render() {
    return (
      <>
        <input ref={this.inputEl} type="text" />
        <button onClick={this.onButtonClick.bind(this)}>Focus</button>
      </>
    );
  }
}
```

## Component with hooks

```js
const FocusInputWithButton = () => {
  const inputEl = useRef(null);

  const onButtonClick = () => {
    inputEl.current.focus();
  };

  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus</button>
    </>
  );
};
```

## Refs Forwarding

Ref have to be fowarded when passed to children.

❌ Does not work !

```js
const FocusInputWithButton = () => {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    inputEl.current.focus();
  };

  return (
    <>
      <button onClick={onButtonClick}>Focus</button>
      <Input ref={inputEl} />
    </>
  );
};

const Input = ({ ref }) => <input ref={ref} type="text" />;
```

✔️ Works !

```js
const FocusInputWithButton = () => {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    inputEl.current.focus();
  };

  return (
    <>
      <button onClick={onButtonClick}>Focus</button>
      <Input ref={inputEl} />
    </>
  );
};

const Input = React.forwardRef((props, ref) => <input ref={ref} type="text" />);
```
