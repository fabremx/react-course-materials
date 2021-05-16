# Class Component Limitations

Here's a example of a complex class component which show us the limitation of class

- Too many state logical and side effect
- It’s often impossible to break down these components into smaller one because the stateful logic is scattered

```js
import React from "react";

export class BooksList extends React.Component {
  subscription;

  constructor(props) {
    super(props);

    this.state = {
      books: [],
      error: false,
    };

    this.filters$ = new Subject();
    this.filters = this.filters$.asObservable().pipe(debounceTime(500));
  }

  async componentDidMount() {
    const user = api.getUserById(this.props.userId);

    if (!user.authenticated) {
      this.setState({ error: true });
      return;
    }

    this.subscription = this.filters.subscribe((filters) => {
      this.applyFilters(filters);
    });

    const books = fetchBooks(this.props.booksIds);
    this.setState({ books });
  }

  componentDidUpdate(prevProps) {
    if (prevProps.booksIds !== this.props.booksIds) {
      fetchBooks(this.props.booksIds);
      this.setState({ books });
    }
  }

  componentWillUnmount() {
    this.subscription.unsubscribe();
  }

  fetchBooks(booksIds) {
    // API call
  }
  applyFilters(filters) {
    // Code
  }

  render() {
    // Render Component
  }
}
```

⚠️ Problems with the code above:

- `componentDidMount` handle too many different logics (user, books, subscription)
- Difficult to break down this composant into small parts (to tests them and reuse them)
