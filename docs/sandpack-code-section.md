# Markdown file

<style>
  article {
    min-height: auto;

    pre:has(> code) {
      outline: 1px solid #eee;
      padding: 0.5rem;
    }

    p > code {
      background-color: #eee;
    }
  }

  hr {
    margin-block: 1rem;
  }

</style>

## Code in markdown

Let's start with a typical code in markdown.

````
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Parcel Sandbox</title>
    <meta charset="UTF-8" />
    <link rel="stylesheet" href="/styles.css" />
  </head>
  <body>
    <h1>Hello world2</h1>
  </body>
</html>
```
````

is rendered as HTML:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Parcel Sandbox</title>
    <meta charset="UTF-8" />
    <link rel="stylesheet" href="/styles.css" />
  </head>
  <body>
    <h1>Hello world2</h1>
  </body>
</html>
```

---

## Code in Sandpack

Adding `!sandpack` suffix to the language ...

````

```html!sandpack
<h1>Hello world</h1>
```

````

... will render it into a sandpack app:

```html!sandpack
<h1>Hello world</h1>
```

Notice that you don't have to write down all HTML, and it still renders it correctly,
naming it as `body.html`.

---

## Working with multiple files

You can wrap multiple code within a `<section>` tag to make it into a single group.

````
<section data-code-section>

  ```html
  <button>Click</button>
  ```

  ```js
  document.querySelector('button').addEventListener('click', () => {
    alert('hello world!');
  });
  ```

  ```css
  button {
    font-size: 1.5rem;
  }
  ```

</section>
````

It is recommended to add `data-code-section` data attribute, where it can receive additional options (more on in later).

<section data-code-section="multiple">

```html
<button>Click</button>
```

```js
document.querySelector('button').addEventListener('click', () => {
  alert('hello world!');
});
```

```js {"name": "console.js", "hidden": true}
console.log('Hello console');
```

```css
button {
  font-size: 1.5rem;
}
```

```css {"name": "hidden.css", "hidden": true}
* {
  position: relative;
}
```

</section>

---

## Naming

### Implicit naming

By default, files will be named `"index"`, followed by it's filetype as an extension.

````
<section data-code-section>

```js
const a = 1 + 2;
```

```css
div {
  display: block;
}
```

</section>
````

renders into:

<section data-code-section>

```js
const a = 1 + 2;
```

```css
div {
  display: block;
}
```

</section>

However, an HTML file without the enclosing `<html><body>` tag will be named as `"body.html"`. An additional `"index.html"` will be included (hidden by default).

<section data-code-section="include:multiple">

</section>

When you have multiple files with same language, it will be numbered.

````
<section data-code-section>

```js
const a = 1 + 2;
```

```js
const b = 2 + 3;
```

</section>
````

<section data-code-section>

```js
const a = 1 + 2;
```

```js
const b = 2 + 3;
```

</section>

---

### Naming explicitly

You can assign names explicitly:

````

```html!sandpack {"name": "app.html"}
<h1>Hello world2</h1>
```

````

```html!sandpack {"name": "app.html"}
<h1>My app</h1>
```

---

## Referencing

Naming is important, because you can _refer_ to it later. \
There are two types of references, `"include"` and `"appendTo"`.

#### `appendTo` field

Use `"appendTo"` field to add to an existing code block.

Remember we have named `app.html` before? We are now appending more content to it.

````
```html!sandpack {"appendTo": "app.html"}

<h2>I am appended</h2>

```
````

is rendered as:

```html!sandpack {"appendTo": "app.html"}

<h2>I am appended</h2>

```

---

#### `include` field

Use `"include"` field just to copy an existing code block.

````

```html!sandpack {"include": "app.html", "name": "app-with-incude.html"}

```

````

```html!sandpack {"include": "app.html", "name": "app-with-incude.html"}

```

`"name"` fields are not mandatory,
but as it gets complicated quickly it may be a good idea to keep it up with unique names.

---

### Naming code sections

You can also name code sections by using the `data-code-section` attribute. It can receive JSON objects:

````
<section data-code-section="{\"name\":\"named-code-section\"}">

```js
console.log('a');
```

```js
console.log('b');
```

</section>
````

<section data-code-section="{\"name\":\"named-code-section\"}">

```js
console.log('a');
```

```js
console.log('b');
```

</section>

You can `"include"`, or `"appendTo"` previous code sections, like we did in code blocks.

```

<section data-code-section="{\"appendTo\":\"named-code-section\"}">

</section>

```

<section data-code-section="{\"appendTo\":\"named-code-section\",\"name\":\"section2\"}">

```js {"name": "index2.js"}
// I have added some comment here
```

```js {"name": "index3.js"}
console.log('new file!');
```

</section>

We can do the same for `"include"` too.

NOTE we need to add a full JSON format in the `data-code-section` attribute, escape the quotations, and should not have any space in between.

---

### Shorthand naming conventions

Putting a full JSON format inside the attribute string can be a headache, so here are some simpler notations:

When you want to...

- _name_ it: pass in a string, e.g `<section data-code-section="section2">`
- _refer_ it: prepend with either `appendTo:` or `include:`, e.g `<section data-code-section="appendTo:named-code-section`>" \
  You can also do `"name:section2"`, which is same as just providing "section2".

- refer it, _and_ have a name: comma separated values are recognized, e.g `<section data-code-section="appendTo:named-code-section,name:section2">`

NOTE since we use colons(`:`) and commans(`,`) as a special syntax, you cannot have them in a filename. \
(for instance, `data-code-section="the_name:my_app"` won't work)

If this shorthand feels all too complex, you can always use the standard JSON notation:

```
<section data-code-section="{\"appendTo\":\"named-code-section\",\"name\":\"section2\"}">

</section>
```

Just don't forget to escape the quotations, and remove spaces.
