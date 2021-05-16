# Hook "useCallback"

Returns a memoized reference of the callback function it is passed.
helpful when passing callback props to highly optimised child components.

## Understand what is reference memorization

```js
function factory() {
  return (a, b) => a + b;
}

const sum1 = factory();
const sum2 = factory();

sum1(1, 2); // => 3
sum2(1, 2); // => 3

sum1 === sum2; // -> false
sum1 === sum1; // -> true
```

In react methods are re-created on each render

```js
function Component() {
  // handleClick is re-created on each render
  const handleClick = () => console.log("Clicked");
}
```

## Bad and Good use of 'useCallback'

`useCallback` doesn’t memoize the function result, it memorize the reference.

❌ BAD use of `useCallback`

```js
function ParentComponent() {
  const onHandleClick = useCallback(() => {
    const special = calculatePi();
  });

  return <SubComponent handleClick={onHandleClick} />;
}
```

Each time the « SubComponent » triggers the onHandleClick.
`calculatePi()` will be triggered and recalculate.

If we wanted to avoid re-calculating Pi we have to use React.memo.

✔️ GOOD use of `useCallback`

```js
function ParentComponent() {
  const onHandleClick = useCallback(() => {
    const special = calculatePi();
  });

  return (
    // Component Memoized
    <LargeList items={items} onItemClick={onItemClick} />
  );
}

function LargeListComponent = ({ items, onItemClick}) = { ... }
export const LargeList = React.memo(LargeListComponent)
```
