# Notes from intro-to-react-v5

## VS code

- Multiple cursors: _click and hold alt, then click again underneath it_

## Emmet

- Generate html structure
  `html:5`
- script
  `script:src`
- link with css
  `link:css`

## Prettier

- run from command line
  `prettier \"src/**/*.{js, html}\"`

## ESLINT

- `extends` are sets of rules and `plugins` are new abilities for eslint
- `rule` - we can turn on/off specific rules

## General

- `package-lock` locks down the versions when you deploy your code
- on production: `npm ci` will use the package-lock insteade of the package.json to install the exactly the correct versions
- Importing - curly braces or not?
  - curly braces: when importing single feature; it's live code inclusion: it includes only the code we want to run (if they packaged it correctly)
  ```javascript
  import { render } from "react-dom";
  ```
- default exports:

```javascript
export default function Pet({something, something});
```

and then

```javascript
import Something from ...
```

otherwise it would be

```javascript
const Pet = { something, something } { ... };
```

```javascript
import { Something } from ...
```

- JSX

  - no JSX: write js to mimic markup
  - JSX: write js directly in html; translates to React code

- Understand React correctly:
  `npm install -D babel-eslint eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react`

  - `babel-eslint` allows eslint to be augumented by Babel (the transpiler). Eslint doesn't understand React out of the box. `eslint-plugin-import` gives some rules about importing and exporting things. `eslint-plugin-jsx-a11y` - simple accessability; things for accesibility (e.g. don't make divs clickable). `eslint-plugin-react` - additional react rules.

- `prop-types`: weak type checking for React

- return ()
  Javascript works so that if it sees a new line, it will end `return` there
  By surrounding the content in parenthesis, JS knows the code is not done and should continue to the next line.
- Statement vs Expression

  `const x = name.toUpperCase();`

  The whole thing is a statement (the whole idea), name.toUpperCase(); is an expression

  - things in `{}` are considered expressions (on the right side of an statement)

- !!! use onChange and onBlur together for better accessibility
  http://www.themaninblue.com/writing/perspective/2004/10/19/

- Why unique keys?
  - no need to rerender everything when we are just e.g. resorting things

### Hooks

- two way data binding is not free in React. Re-rendering doesn't force change (e.g. input onChange is explicit)

```javascript
const [location, setLocation] = useState("Seattle, WA");
```

- this is also destructuring, we know this is going to be an array; we know the first item is always the state, the second is the updater
- Here "Seattle, WA" is the default state.
- `location` is the current state of location, setLocation is the updater for that piece of state
- !!! render functions for components: every time you update your application, it reruns all of the renders. So make sure the hooks are not doing anything extraneus, e.g. updating any function, no side effects. Should be focused on rendering something.
- all hooks begin with `use` -> useEffect, useState, etc
- you can spare yourself setting the state
- !!! hooks never go inside `if` statements or `for` loops!!! They are keeping track of the order in which we are creating hooks, a.k.a the order we are calling these hooks

#### useEffect

- takes place of several of the lifecycle hooks, e.g. componentDidMount, componentWillUnmount, componentDidUpdate
- it's disconnected from when the render is actually happening
-

#### Custom hook

```javascript
const useDropdown = (label, defaultState, options) => {
  const [state, setState] = useState(defaultState);
  const id = `use-dropdown-${label.replace(" ", "").toLowerCase()}`;
  const Dropdown = () => (
    <label htmlFor={id}>
      {label}
      <select
        id={id}
        value={state}
        onChange={(e) => setState(e.target.value)}
        onBlur={(e) => setState(e.target.value)}
        disabled={options.length === 0}
      >
        <option>All</option>
        {options.map((item) => (
          <option key={item} value={item}>
            {item}
          </option>
        ))}
      </select>
    </label>
  );

  return [state, Dropdown, setState];
};
```

Usage:

````javascript
const [breed, BreedDropdown] = useDropdown("Breed", "", breeds);

...
<BreedDropdown />```
````

## Strict mode
- future proof you application
- if you are using one of the unstable APIs, it will trigger warnings

## Class components 
- functional components === stateless components
- componentDidMount - runs once and then it stops; useful for ajax requests
- super(props) calls the constructor on the parent class (which is often React.Component)
```
    constructor(props) {
        super(props);
    }
```
- `getDerivedStateFromProps`
  - invoked right before calling the render method, both on the initial mount and on subsequent updates
  - it should return an object to update the state, or null to update nothing
  - used for when the state depends on changes in props

## Babel
- `"@babel/preset-react"` => brings all the things you need to transiple react
- `"@babel/preset-env"` => will transpile the code to the env you specify
- `"@babel/plugin-proposal-class-properties"` => allows us to use new syntax

## React context
In App.js
```js
const App = () => {
  const themeHook = useState("darkblue");
  return (
    <React.StrictMode>
      <ThemeContext.Provider value={themeHook}>
        <div>
          <header>
            <Link id="something-important" to="/">Adopt me!</Link>
          </header>
          <Router>
            <SearchParams path="/" />
            <Details path="/details/:id" />
          </Router>
        </div>
      </ThemeContext.Provider>
    </React.StrictMode>
  );
};
```

In a child component:
```js
  <ThemeContext.Consumer>
      {(themeHook) => (<button style={{ backgroundColor: themeHook[0] }}>
          Adopt {name}
      </button>)}
  </ThemeContext.Consumer>
```