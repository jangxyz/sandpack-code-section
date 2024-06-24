# Transition to and from `display: none`

https://www.youtube.com/watch?v=vmDEHAzj2XE

```css
dialog {
  display: none;
  opacity: 0;
  transition: opacity 1s;
}
dialog[open] {
  display: block;
  opacity: 1;
}
```

---

## Youtube Contents

<details open>

<summary>Contents</summary>

- [00:00](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=00m00s) - Introduction
- [00:40](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=00m40s) - Using keyframes to animate the display property
- [04:45](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=04m45s) - What you might try to get transition to work
- [05:58](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=05m58s) - What is a discrete animation
- [08:13](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=08m13s) - Open Web Docs
- [09:00](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=09m00s) - Using transitions to go to and from display: none
- [10:55](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=10m55s) - CSS Demystified
- [11:30](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=11m30s) - Using @starting-style
- [12:30](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=12m30s) - Easily have different transitions in each direction
- [13:52](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=13m52s) - Browser support and progressive enhancements
- [16:12](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=16m12s) - Make sure you include overlay for popups and dialogs
- [17:25](https://www.youtube.com/watch?v=vmDEHAzj2XE&t=17m25s) - The problem with peudo-elements, including ::backdrop

[We can now transition to and from display: none](https://www.youtube.com/watch?v=vmDEHAzj2XE)

</details>

---

## Preparing the Modal

<section data-code-section="modal">

```html {"name": "body.html"}
<button>Open the modal</button>
<dialog>
  <h2>A pretty standard modal</h2>
  <button>Close the modal</button>
</dialog>
```

```js {"name": "index.js"}
const dialog = document.querySelector('dialog');
const showDialog = document.querySelector('button');
const closeDialog = dialog.querySelector('button');

showDialog.addEventListener('click', () => {
  dialog.showModal();
});

closeDialog.addEventListener('click', () => {
  dialog.close();
});
```

```css {"name": "index.css"}
dialog {
  display: none; /* User-agent style */
}
dialog[open] {
  display: block; /* User-agent style */
}

dialog[open]::backdrop {
  display: none;
}
```

```css {"name": "base.css"}
@layer general-styling {
  html {
    font-family: system-ui;
    color-scheme: dark light;
    font-size: 2rem;
  }

  html:has(#light:checked) {
    color-scheme: light;
  }
}
```

</section>

---

## 1. Animations

---

### Animation (1) - appear

Animation always works ...

<section data-code-section="animation-1-appear" data-code-section-name="animation-1-appear" data-code-section-append-to="modal">

```css
/* ... */

@keyframes appear {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

dialog[open] {
  display: block; /* User-agent style */
  animation: appear 1s;
}
```

</section>

... on appear, but not on disappear.

---

### Animation (2) - vanish

Does not work with reverse key frames.

<section data-code-section="animation-2-vanish">

```css {"appendTo": "index.css", "name": "animation-2.css"}
/* ... */

@keyframes vanish {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}

dialog {
  display: none;
  animation: vanish 1s;
}
```

```html {"include": "body.html"}

```

```js {"include": "index.js"}

```

```css {"include": "base.css"}

```

</section>

---

### Animation (3) - vanish works!

The key is `display: block;`

<section data-code-section>

```css {"appendTo": "index.css", "name": "animation-3.css"}
/* ... */

@keyframes vanish {
  from {
    display: block;
    opacity: 1;
  }
  to {
    display: none;
    opacity: 0;
  }
}

dialog {
  display: none;
  animation: vanish 1s;
}
```

```html {"include": "body.html"}

```

```js {"include": "index.js"}

```

```css {"include": "base.css"}

```

</section>

`display` never worked as 'discrete-animation', but instantly.

---

## 2. Transitions

Now, let's make it work with transitions.

Reset:

<section data-code-section="include:modal">

</section>

---

### Transitions (1)

Let's try: `transition: opacity 1s;`

<section data-code-section>

```css {"name": "transition-1.css", "appendTo": "index.css"}
/* ... */

dialog {
  display: none; /* User-agent style */
  opacity: 0;
  transition: opacity 1s;
}
dialog[open] {
  display: block; /* User-agent style */
  opacity: 1;
}
```

```html {"include": "body.html"}

```

```js {"include": "index.js"}

```

```css {"include": "base.css"}

```

</section>

> if there's a prperty that's changing that is considered as a discrete animation -- such as a display block -- it will prevent any transitions from working \
> -- [5:51s](https://youtu.be/vmDEHAzj2XE?si=2kMfQYF34M8OGGRK&t=5m51s)

---

### Transition (2): allow-discrete

[transition-behavior](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-behavior) has a value `allow-discrete`, let's use that:

> allow-discrete: Transitions will be started on the element for discrete animated properties. \
> -- [allow-discrete, MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-behavior#allow-discrete)

<section data-code-section="appendTo:modal,name:transition-2">

```css
/* ... */

dialog {
  display: none; /* User-agent style */
  opacity: 0;
  transition-property: display opacity;
  transition-duration: 1s;
  transition-behavior: allow-discrete;
}
dialog[open] {
  display: block; /* User-agent style */
  opacity: 1;
}
```

</section>

allow-discrete option will allow transition, even if there is a discrete animation property.

여전히 닫을 때만 적용되고 있음.

---

### Transition (3): @starting-style

`@starting-style` 라는걸 써보자.

> ... used to define starting values for properties set on an element that you want to transition from when the element receives its first style update \
> -- [@starting-style, MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style)

<section data-code-section="appendTo:modal">

```css
/* ... */

dialog {
  display: none; /* User-agent style */
  opacity: 0;
  transition-property: display opacity;
  transition-duration: 1s;
  transition-behavior: allow-discrete;
}
dialog[open] {
  display: block; /* User-agent style */
  opacity: 1;

  @starting-style {
    opacity: 0;
  }
}
```

</section>

성공!

---

### Transition(4) - @starting-style와 transition과의 관계

둘이 어떤 관계지? 잠깐 실험을 해보자.

<section data-code-section="appendTo:modal" data-code-section-name="transition-4">

```css
/* ... */

dialog {
  display: none; /* User-agent style */
  opacity: 0;
  translate: 0 -25vh; /* <-- on close */
  transition-property: display opacity;
  transition-duration: 1s;
  transition-behavior: allow-discrete;
}

dialog[open] {
  display: block; /* User-agent style */
  opacity: 1;
  translate: 0 0; /* <-- on show */

  @starting-style {
    opacity: 0;
    translate: 0 -25vh; /* <-- on open */
  }
}
```

</section>

세 군데 추가함.

결과: 열 때 위에서 내려오고, 닫을 때 다시 위로 올라간다.

- `@starting-style`에 들어 있는건 <u>막 열 때의 시작값</u>: **on open**
- `dialog[open]`에 들어 있는 건 <u>열려 있을 때의 위치</u>: **on show**
- `dialog` 에 들어 있는 translate는 <u>닫을 때의 목표값</u>: **on close**

만약에 `dialog`에 있는 translate를 "25vh"로 바꾸면, \
열 때는 위에서(-25vh) 아래(0)로 내려오고, 닫을 때는 다시 아래(25vh)로 내려간다.

---

### 브라우저 지원

- `transition-behavior: allow-discrete`: FF, Safari 지원 안 함 \
  https://caniuse.com/mdn-css_at-rules_starting-style

- `@starting-style`: FF는 지원 안 함 \
  https://caniuse.com/mdn-css_properties_display_is_transitionable

---

### Others - overlay property

transition을 할 때는 transition-property에 `overlay`를 넣어주는 것이 좋다.

---

### Others - Backdrop을 꾸며보자

<section data-code-section-appendTo="transition-4">

```css
dialog::backdrop {
  opacity: 0;
  background-image: linear-gradient(45deg, red, blue);

  transition-property: opacity display overlay;
  transition-duratino: 1s;
  transition-behavior: allow-discrete;
}

dialog[open]::backdrop {
  opacity: 0.75;
}
@starting-style {
  dialog[open]::backdrop {
    opacity: 0;
  }
}
```

</section>

---

## 정리

<div style="display: flex;">

<div>

- Animation:

  - `@keyframes`에 `display: none;` 을 넣을 수 있다.

    <details>
      <summary>Code</summary>

    ```css
    @keyframes vanish {
      from {
        display: block;
        opacity: 1;
      }
      to {
        display: none;
        opacity: 0;
      }
    }

    dialog {
      display: none;
      animation: vanish 1s;
    }
    ```

    </details>

</div>
<div>
- Transition:

- `transition-behavior: allow-discrete;` property를 넣어준다.
- `@starting-style` rule을 넣어준다.
- `overlay`도 가급적 추가해주자.

  <details>
    <summary>Code</summary>

  ```css
  dialog {
    display: none; /* User-agent style */
    opacity: 0;
    transition-property: opacity display overlay;
    transition-duration: 1s;
    transition-behavior: allow-discrete;

    translate: 0 -25vh; /* <-- on close */
  }

  dialog[open] {
    display: block; /* User-agent style */
    opacity: 1;
    translate: 0 0; /* <-- on show */

    @starting-style {
      opacity: 0;
      translate: 0 -25vh; /* <-- on open */
    }
  }
  ```

  </details>

</div>
</div>

---

## Reference

- https://developer.chrome.com/blog/entry-exit-animations
- https://developer.mozilla.org/en-US/docs/Web/CSS/transition-behavior#allow-discrete
  - https://caniuse.com/mdn-css_properties_display_is_transitionable
- https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style
  - https://caniuse.com/mdn-css_at-rules_starting-style
