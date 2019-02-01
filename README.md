# css-2-React
=======================

# Installation
=======================

```
npm install css-2-react
```

# Usage
=======================

1. Include the css-2-react-loader in your webpack config like this:
```
```

2. Write any CSS you can think of, in ordinary .css files...
```css
  .foo {
      color: green,
  }
```

3. Import it into a .js file in a React app, and use it like you would any other component
```js
  import Foo from './foo.css'

  export default () => (
    <Foo>Hello</Foo>
  )
```

4. Magic!
```html
  <html>
    <div style="color: green">
      Hello
    </div>
  </html>
```

## How it works
css-2-react turns CSS files into minimal React components. Its use a naming convention to generally PascalCase and turn your selector into a component name. The example above compiled to:
```js
const Foo = (props) => <div style={{ color: 'green' }} {...props}/>;
```

## Specifying component name
Include the attribute `component` in your selector. This takes precedence over _all other naming conventions_.

```css
[component="Foo"] {
  color: 'green'
}
```
...produces...
```js
const Foo = (props) => <div style={{ color: 'green' }} {...props} />;
```


## Specifying HTML tags
Include an element selector in your CSS to control the output HTML element tag.

```css
a[component="Foo"] {
  color: green;
}
```
...produces...
```js
const Foo = (props) => <a style={{ color: 'green' }} {...props} />;
```

## Adding dynamic props
In CSS, psuedo-classes are used to describe temporary states of DOM elements. In css-2-react, pseudo-classes are mapped to property names.

```css
a[component="Foo"] {
  color: green;
}

a[component="Foo"]:hover {
  color: orange;
}
```
...produces...
```js
const Foo = (props) => {
  let style = { color: 'green' };

  if (props.hover) {
    style = {
      ...style,
      { color: 'orange' }
    };
  }

  return <a style={style} {...props} />
};
```

## Media queries
This first version of css-2-react will likely compile CSS to plain style objects in React components. That approach won't work with media queries, so the implementation will have to manage responsiveness with JS width providers.

To assist with managing responsive states, css-2-react will turn unique media query blocks into properties for the wrapped component.

```css
.foo { color: green }
@media (min-width: 320px and max-width: 640px) {
  .foo { color: orange }
}
```
...produces...
```js
const Foo = (props) => {
  let style = { color: 'green' };

  if (props.minWidth320PxAndMaxWidth640Px) {
    style = {
      ...style,
      { color: 'orange' }
    };
  }

  return <a style={style} {...props} />
};
```

css-2-react could compile CSS to JS for component structure, while transpiling the CSS to keep support for some native CSS functionality. Help getting it off the ground would be welcome.

## What happens if...

### I have multiple classes in a selector?
Multiple classes in a selector, when chained, will create a new component whose name is a superset of the class names.

```css
.foo.bar { color: green } /* turns into <FooBar/> */
```


### I have multiple rules in the same css file?

### I have multiple rules that conflict with each other?

### I use custom attribute selectors, like `[href^="ftp"]`?

### I use descendant or sibling selectors, like `p + p`?
