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

This is the underlying principle.

1. CSS selectors are descriptors of DOM elements.
2. This description is typically used to create a list of matching DOM elements, but it can be reverse-engineered to create a DOM element that matches the description. That's exactly what we're trying to do.
3. CSS IDs and classes are user-defined names for user-defined objects, so they're analagous to component names.
4. CSS element selectors clearly map to DOM elements.
5. CSS pseudo-classes are typically used for transient states, so we'll provide mappings for props to directly trigger them.
6. If in doubt, we can use a camel-cased name generator to make component names from long selectors.
6. TBD: CSS pseudo-elements generate semi-uncontrolled child DOM elements. The tricky thing here is that only 2 are supported, and their names determine their positions.
7. TBD: Media queries are a pretty complex layer. We could simply support them as CSS, provide an interface to match media queries through props, or munge them into unique component variations.

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
_TBD: I'm not super happy with this pattern. File an issue if you have a better idea!_

This first version of css-2-react will likely compile CSS to plain style objects in React components. That approach won't work with media queries, so the implementation will have to manage responsiveness with JS width providers.

To assist with managing responsive states, css-2-react will turn unique media query blocks into properties for the wrapped component.

```css
.foo { color: green }
@media (min-width: 320px) and (max-width: 640px) {
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

  return <div style={style} {...props} />
};
```

css-2-react could compile CSS to JS for component structure, while transpiling the CSS to keep support for some native CSS functionality. Help getting it off the ground would be welcome.

## What happens if...
CSS has so much rich functionality. Whenever possible, css-2-react should be true to the spirit of CSS.
Where CSS describes for the purpose of identifying, css-2-react describes for the purpose of creating.

pseudo-classes and pseudo-elements generate DOM elements and states in CSS, so it seems natural to adapt them for that purpose in React.

Some other selectors, like attributes

### I have multiple classes in a selector?
Multiple classes in a selector, when chained, will create a new component whose name is a superset of the class names.

To preserve some semblance of CSS semantics, we normalize the order of the chained selector pieces.

``

```css
/*
  id → classname → attribute → element
  each element type is sorted A-Z
*/
.foo.bar { color: green } //
```
...produces...
```js
const BarFoo = (props) => (<div style={{ color: 'green' }} {...props} />);
```

### I have multiple rules in the same css file?
Each rule with a unique selector becomes its own export.

### I have multiple rules that conflict with each other?
Styles are appended to earlier styles.
Just like in CSS, the last definition for a particular property wins.

### I use custom attribute selectors, like `[href^="ftp"]`?
Custom attribute selectors are turned into properties on Components.
Styles attached to the attribute selector apply only if the attribute exists (and matches the pattern, if one is provided).

_TBD:
The downside of this approach is that there's no difference between `.x[hover]` and `.x:hover`.
A more literal `:hover` => `props[':hover'] === true` might be better for handling pseudo-classes.
(Perhaps treating pseudo-classes as bare props should be opt-in.)_

```css
.x[foo] { color: green }
.x[foo="bar"] { color: blue }
```
...produces...
```js
const X = (props) => {
  let style = {};

  if (props.foo) {
    style = { ...style, { color: 'green' } };
  }

  if (prop.foo === 'bar') {
    style = { ...style, { color: 'blue' } };
  }

  return <div style={style} {...props} />
};
```

### I use descendant or sibling selectors, like `p + p`?
