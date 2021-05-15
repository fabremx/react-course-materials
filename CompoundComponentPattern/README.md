# Compound component pattern

This pattern focuses on passing down the state of the parent component to its children.

## 📘 Context

I want to pass down the parent state to its children like this.

```js
<Form isDisabled={isDisabled}>
  <Input isDisabled={isDisabled} />
  <Button isDisabled={isDisabled} />

  <FormGroup isDisabled={isDisabled}>
    <Input isDisabled={isDisabled} />
    <Button isDisabled={isDisabled} />
  </FormGroup>
</Form>
```

## 📌 Problem

This code above works, but forces us to pass our `isDisabled` prop through each children. Even those who don't need it.

## 🔑 Solution

Two solutions are possible:

- React.cloneElement
- Using context API

### React.cloneElement

We are going to extends children components props with `isDisabled`

```js
export const Form = (props) => {
  const extendedProps = {
    isDisabled: props.isDisabled,
  };

  const children = React.Children.map(props.children, (child) => {
    return React.cloneElement(child, extendedProps);
  });

  return <form>{children}</form>;
};
```

Our children components will receivethe prop `isDisabled`

```js
export const Input = (props) => {
  return <input type="text" disabled={props.isDisabled} />;
};

export const Button = (props) => {
  return <button disabled={props.isDisabled}>go</button>;
};
```

⚠️ **Beware** : This methods works only for direct children

```js
<Form disabled={disabled}>
  {/* Works !*/}
  <Input />
  <Button />

  <FormGroup>
    {/* Does not work ! */}
    <Input />
    <Button />
  </FormGroup>
</Form>
```

💡 Prefer the Context API method.

### Context API

```js
const FormContext = React.createContext(null);

const Form = (props) => {
  const [isDisabled, setIsDisabled] = useState(true);

  return (
    <FormContext.Provider value={{ isDisabled }}>
      <button onClick={() => setIsDisabled(!isDisabled)}>Switch</button>
      {props.children}
    </FormContext.Provider>
  );
};
```

Our children components will receivethe prop `isDisabled` through the hook `useContext` now.

```js
const Button = () => {
  const { isDisabled } = useContext(FormContext);
  return <button disabled={isDisabled}>button</button>;
};
const Input = () => {
  const { isDisabled } = useContext(FormContext);
  return <input disabled={isDisabled} />;
};
```
