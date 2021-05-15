# Render props

## Layout exemple

#### Context

We have a `Layout` component which handle logic to set visible children component or not.
Children component can be different component

```js
const Layout = () => {
  const [isOpen, setIsOpen] = React.useState(false);

  return (
    <div className="layout">
      <button onClick={() => setIsOpen(!isOpen)}> Menu </button>
      {isOpen && <p>Layout message visible !</p>}

      {/* Display Page1, Page2, ... components if isOpen = true */}
    </div>
  );
};
```

#### Problem

We want to avoid bad solution like this:

**BAD solution**

```js
const Layout = ({ type }) => {
  const [isOpen, setIsOpen] = React.useState(false);

  return (
    <div className="layout">
      <button onClick={() => setIsOpen(!isOpen)}> Menu </button>
      {isOpen && <p>Layout message visible !</p>}

      {isOpen && type === "Page1" && <Page1 />}
      {isOpen && type === "Page2" && <Page2 />}
    </div>
  );
};
```

We don't want that `Layout` handle the logic to display or not children
This is also broke the SOLID "Open-close principle"

#### Solution

Pass children component as children

```js
<Layout>
  <Page1 />
</Layout>;

// OR

<Layout>
  <Page2 />
</Layout>;
```

Let's change `Layout` component

```js
const Layout = ({ children }) => {
  const [isOpen, setIsOpen] = React.useState(false);

  return (
    <div className="layout">
      <button onClick={() => setIsOpen(!isOpen)}> Menu </button>
      {isOpen && <p>Layout message visible !</p>}

      {isOpen && children}
    </div>
  );
};
```

With for exemple a children `Page1` and `Page2`

```js
const Page1 = () => (
  <div className="page1">
    <p> I'm visible (Page1) ! </p>
  </div>
);

const Page2 = () => (
  <div className="Page2">
    <p> I'm visible (Page2) ! </p>
  </div>
);
```

This is a good start. However `Layout` component still handle display logic of children.
Let's change that

```js
const Layout = ({ children }) => {
  const [isOpen, setIsOpen] = React.useState(false);

  return (
    <div className="layout">
      <button onClick={() => setIsOpen(!isOpen)}> Menu </button>
      {isOpen && <p>Layout message visible !</p>}

      {children(isOpen)}
    </div>
  );
};

const Page1 = ({ isOpen }) => (
  <div className="page1">{isOpen && <p> I'm visible (Page1) ! </p>}</div>
);
const Page2 = ({ isOpen }) => (
  <div className="Page2">{isOpen && <p> I'm visible (Page2) ! </p>}</div>
);
```

## Form component exemple

#### Context

We have a `ContactForm` component which handle

- Display fields
- Handle values entered by user

```js
class ContactForm extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      name: props.name || "",
      email: props.email || "",
    };
  }

  render() {
    return (
      <form>
        <input
          value={this.state.name}
          onChange={(e) => {
            this.setState({ name: e.target.value });
          }}
        />
        <input
          value={this.state.email}
          onChange={(e) => {
            this.setState({ email: e.target.value });
          }}
        />
      </form>
    );
  }
}
```

We decided to create an other component `BooksForm` with other fields to display but which still handle the values entered by user.

#### Problem

Our code will not be DRY if we duplicate the `ContactForm` component and only change the fields to display.

#### Solution

Let's create a `FormManager` component which only handle the values entered by user.
`ContactForm` and `BooksForm` will only display different fields.

```js
const FormManager = ({ initialValues, children }) => {
  const [values, setValues] = useState({ ...initialValues });

  const setValue = (name, value) => {
    if (name in values) {
      setValues({
        ...values,
        [name]: value,
      });
    }
  };

  return children({
    values,
    setValue,
  });
};
```

Here the code of `ContactForm`

```js
function ContactForm() {
  return (
    <>
      <h1>Contact Form</h1>

      <FormManager initialValues={{ name: "", email: "" }}>
        {({ values, setValue }) => (
          <form>
            <input
              value={values.name}
              onChange={(e) => {
                setValue("name", e.target.value);
              }}
            />
            <input
              value={values.email}
              onChange={(e) => {
                setValue("email", e.target.value);
              }}
            />
          </form>
        )}
      </FormManager>
    </>
  );
}
```
