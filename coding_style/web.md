# Web (Player! Web, Player! Signage, Player! Widget)

We follows a simple set of coding style conventions:

* Use American English. See [a list of American and British English spelling differences here](http://en.wikipedia.org/wiki/American_and_British_English_spelling_differences).
* Use ES2015+ and JSX. See [Javascript style guides](https://github.com/airbnb/javascript) and [React style guides](https://github.com/airbnb/javascript/tree/master/react).
* Use CSS Modules and SCSS. See [CSS style guides](https://github.com/stylelint/stylelint-config-standard).

## Check Your Code

Just Run

    yarn run lint

## Code formatting

Use [Prettier](https://github.com/prettier/prettier)

## Coding style

### extension

* `.js` is only standard JS.
* `.jsx` is file contains JSX.
* `.test.js` is unit test.
* `.test.e2e.js` is e2e test.
* `.worker.js` is web worker.

### action type

* `const FOO_BAR = 'FOO_BAR';`

### src/components

* directory structure
```
atoms/FooBar
  |- FooBar.jsx
  |- FooBar.scss
  |- index.js`
```

### src/containers

* file name: `FooBarContainer.jsx`
* container name: `FooBarContainer`

### src/hooks

* file name: `useFooBar.js`
* hooks name: `useFooBar`

### src/selectors

* file name: `fooBar.js`
* selector name: `fooBarSelector`

### tests

* `describe` : grouping function and condition
* prefer `it` instead of `test`

ex) Button.test.jsx

```
describe('Button', () => {
  it('renders button', () => {
    const { button } = setup(props);
    expect(button).toBeDefined();
  });

  it('calls onClick function', () => {
    const { button } = setup(props);
    fireEvent.click(button);
    expect(props.onClick).toHaveBeenCalledTimes(1);
  });

  it('matches its snapshot', () => {
    const { asFragment } = setup(props);
    expect(asFragment()).toMatchSnapshot();
  });
});
```
