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
css-2-react turns CSS files into minimal React components. We use a naming convention to generally PascalCase and turn your selector into a component name. The example above compiled to:
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
In CSS, psuedo-classes are used to describe temporary states of DOM elements. In css-2-js, pseudo-classes are mapped to property names.

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

## What happens if...

### I have multiple classes in a selector?

### I have multiple rules in the same css file?

### I have multiple rules that conflict with each other?

### I use custom attribute selectors, like `[href^="ftp"]`?

### I use descendant or sibling selectors, like `p + p`?
