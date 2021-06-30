# FE-guidelines

## General

## Why is this `!important`?

-   Build and maintain products for a reasonable length of time
-   Have developers of differing abilities and specialisms
-   Have a number of different developers working on a product at any given time
-   On-board new staff easily
-   Have a number of codebases that developers dip in and out of

## Anatomy of a rule set

### Example rule set

```
[selector] {
  [property]: [value];
}
```

### Do

```
.foo,
.bar {
  display: block;
  background-color: green;
  color: red;
}
```

-   Each selector on its own line
-   Space before the opening bracket (`{`)
-   Properties and values on the same line
-   space after property-value delimiting colon (`:`)
-   Each declaration on its own line
-   Closing bracket on its own line (`}`)
-   Each declaration indented by 2 spaces
-   Trailing semi-colon at the end of each declaration
-   Properties ordered A-Z (easier to read)

There may be situations where this does not work. For example:

```
.foo {
  transition: background-color .3s, color .3s, transform .3s;
}
```

Could be easier to read with each transition property being on its own line

```
.foo {
  transition: background-color .3s,
              color .3s,
              transform .3s;
}
```

## Don't repeat yourself

### Don't

```
.foo {
  background-color: #fff;
  border: 1px solid #000;
  color: #000;
}

.bar {
  background-color: #fff;
  border: 1px solid #000;
  color: #000;
}
```

### Do

```
.btn {
  background-color: #fff;
  border: 1px solid #000;
  color: #000;
}
```

-   Keep things DRY, aim to use standard classes so you can re-use your code
-   SASS includes and extends can encourage poor coding practice, just because it's one line in your code doesn't mean it is when exported
-   If you're applying the same include or styles on lots of selectors, create a class and apply it in HTML to keep things DRY
-   This is where defined h1, h2, p etc. in Photoshop/XD files would be really useful so we can establish reuseable styles

### Exceptions

Sometimes it's still better to KISS (Keep It Simple, Stupid) than DRY.

## IDs

![don't](https://i.imgflip.com/2wgxip.png)

### Don't

```
#foo {
  background-color: #000;
}
```

### Do

```
// Use classes
.foo {
  background-color: #000;
}

// If you want to have a unique identifier the you could use something like this (even though its ugly)
.\#foo {
  background-color: #000;
}

// When being forced to use IDs, use lower specificity (same as class)
[id="foo"] {
  background-color: #000;
}
```

-   Don't use IDs for styling, ever.
-   They're overly specific and trump classes in the order of CSS rendering

### Exceptions

Sometimes you may have to use ID because you're working with apps or code you can't otherwise edit. In this case, and only as a last resort, use IDs. (see examples above)

## `!important`

### Don't

```
.foo {
  color: red !important;
}
```

-   Never use !important if it can be avoided
-   Add inline comments above the property explaining why if you have to use !important

### Exceptions

-   Sometimes using 3rd party code/libraries you have to override their specific styles using `!important`. However consider doubling specificity (see example below)
-   Utility classes are very specific so could use `!important`

### Doubling Specificity

```
.foo.foo {
// This style will override `.foo` styles
}
```

-   This works because it's more specific than just the class name once, but won't override inline styles
-   Consider using BEM modifiers where possible. Document any oddities with inline comments

## Longhand properties

### Don't

```
.foo {
  background: url("/path/to/image.jpg") no-repeat 50% 50% #fff;
  font: 2rem/1.6 "Helvetica", sans-serif;
}
```

### Do

```
.foo {
  background-color: #fff;
  background-image: url('/path/to/image.jpg');
  background-position: 50% 50%;
  background-repeat: no-repeat;
  font-family: 'Helvetica', sans-serif;
  font-size: 2rem;
  line-height: 1.6;
}
```

-   Avoid shorthand for background and font properties
-   For these properties the non-described properties automatically are set to none/default/0 which causes issues
-   The code is more verbose when making responsive changes

### Non-described properties

By setting `background: red;` we actually get the following properties set (in chrome)

```
.foo {
  background-image: initial;
  background-position-x: initial;
  background-position-y: initial;
  background-size: initial;
  background-repeat-x: initial;
  background-repeat-y: initial;
  background-attachment: initial;
  background-origin: initial;
  background-clip: initial;
  background-color: red;
}
```

This is doing a lot more than just setting the intended `background-color` property so try to use longhand declarations where possible.

### Exceptions

Feel free to use shorthand properties for `margin`, `padding`, `border`, and `transform`.

## Property order

### Do

```
.foo {
  // Local variables
  $local-variables: 1rem;

  // Extends
  @extend %button;

  // Includes
  @include grid();
  @include column(6);

  // Properties in alphabetical order
  background-color: transparent;
  border: 0;
  display: flex;
  padding: 0;

  // Pseudo-elements
  &::before {
    content: '';
  }

  // Nested elements
  &__bar {
    margin: 0;
  }

  // Direct descendants
  > .baz {
    padding: 0;
  }

  // Sibling selectors
  + .bam,
  ~ .bam {
    padding-left: 0;
  }

  // Pseudo-selectors
  &:focus,
  &:hover {
    box-shadow: 0 0 5px color(blue, 0.3);
  }

  // Modifiers
  &--big {
    width: 100%;
  }

  // Stateful namespaces
  &.is-open {
    display: block;
  }

  // Parents
  .parent & {
    display: none;
  }
}
```

1. Local variables (`$local_margin`)
2. Extends (`@extend %grid`)
3. Includes (`@include grid()`)
4. Properties (`background-color`)
5. Pseudo-elements (`&::before`, `&::placeholder` etc.)
6. Nested elements (`&__bar`)
7. Direct descendants (`> .baz`, `+ .baz`)
8. Pseudo-selectors (`&:hover`), this way they can change nested elements
9. Modifiers (`&--big`), this gives them precedence over all nested elements
10. Stateful namespaces for temporary states of interactivity (`.is-active`, `.is-open` etc)
11. Parents (`.parent &`), so that they have the greatest priority

**NOTE - no need to add vendor prefixes as this should be done with a build tool (GULP, webpack etc)**

## Media queries

---

### WIP - This section requires discussion/workshops

-   inline or at the bottom/different file
-   mobile first or desktop down (design dependent)
-   using breakpoints or static values (or both)
-   adaptive rather than fluid width (container jumps down in size)
-   should we be using tailwind style mediaqueries for utilities? EG `.u-text-center:md`?

---

## Pseudo-elements & pseudo-selectors

### Don't

```
.foo {
  &:placeholder {...}

  &:after {...}
}
```

### Do

```
.foo {
  &::after {...}

  &::placeholder {...}

  &:hover {...}
}
```

-   Pseudo-elements should use `::` (after, before, first-letter, first-line, marker, placeholder, and selection)
-   Pseudo-selectors should use `:` (hover, focus, active, nth-child etc.)
-   This helps differentiate between an element and a selector

## Formatting

```
/**
 * Component: Example component
 *
 * Example description
 */

.foo {
  @include animation(background-color);
  @include visually-hidden();
  animation-delay: 0s;

  // Add inline comments above the line
  left: calc(100% - (#{$gutter-width} * 2));
  margin: 0;
  opacity: 0.4;
  width: (100% / 3);
}
```

-   Include a doc-block/intro-block if necessary to explain anything not self documenting or hacky
-   Wrap calculations in brackets for readability
-   Put spaces between the values and the mathematical operators
-   Put multiplying and dividing values after the starting value
-   Use lowercase for selectors and properties
-   Use `//` for inline commenting not the block level `/* */`
-   Inline comments should start on a new line preceding the property/issue they're describing
-   Use sentence case for your comments
-   Do not end with a full stop
-   Specify units on zero duration times
-   Don't specify units on zero length values (eg `0px` should be `0`)
-   Add a leading zero for decimal places
-   Don't go to more than three decimal places, the fewer the better (some browsers do not read more than 2 decimal places anyway)
-   Wrap calculations in brackets (`(100% / 3)`)
-   Include parentheses on mixins regardless if there are no arguments

## Magic numbers

### Don't

```
.foo {
  left: 23px;
}
```

### Do

```
.foo {
  // 10px because of font height
  left: ($$gutter-width - 10px - ($nav-height / 2));
}

.foo {
  // This values matches the sum of widths above it
  left: 23px;
}
```

-   Avoid using "magic numbers" that "just work"
-   Use calculations based on existing values (or variables)
-   If you have to set a value then add a comment explaining why this number "just works"

## Variables

When first setting up your project you should go through and define a series of variables to help with the maintenance of the project, it's a lot easier to change the property of one variable rather than hunting for all appearances of, say, a font-family.

Common properties that benefit from variables are:

-   border-radius
-   color
-   font-family
-   font-weight
-   margin (gutters, grid gutters)
-   transition (duration, easing) – consider a mixin

**NOTE - this may (well should) be built into our FE framework**

### Don't

```
.foo {
  color: red;
  font-family: Lato, sans-serif;
}
```

### Do

```
.foo {
  color: $red;
  font-family: $primary-font-stack;
}
```

-   Variables should be defined in a single file/folder
-   Variable names should be descriptive

**NOTE - maybe look into defining [global variables](https://github.com/we-make-websites/wmw-coding-guidelines/blob/master/css/README.md#variable-naming)**

## Colours

### Don't

```
// Don't use system colours
$red: RED;

// Don't use uppercase HEX
$red: #FF0000;

// Don't use hsl (difficult to read)
$red: hsl(0, 100%, 50%);
```

### Do

```
// Use lowercase HEX colours
$red: #ff0000;
$cornflower-blue: #6195ed;

// Use
$red: rgb(255, 0, 0);
$cornflower-blue: rgb(97, 149, 237);

// Or
$red: rgba(255, 0, 0, 0.5);
$cornflower-blue: rgba(97, 149, 237, 0.5);
```

-   Colours should be named using the [name that color](https://marketplace.visualstudio.com/items?itemName=guillaumedoutriaux.name-that-color) VS code extension or [website](https://chir.ag/projects/name-that-color/#6195ED) (unless they're named in the client's brand guidelines)
-   If you have two or more colours similar enough to share the same name then suffix them with a number where the 1 is the darkest variant of the colour
-   HEX colours should be written in lowercase as it is easier to read

**NOTE - if using rgba colours maybe use original colour name EG `$black-rgba-5: rgba($black, .5)`**
**NOTE - maybe decide if we use all HEX or all rgb/rgba**

## BEM

---

### WIP - This section requires discussion/workshops

-   https://cssguidelin.es/#bem-like-naming
-   https://github.com/we-make-websites/wmw-coding-guidelines/blob/master/css/README.md#bem-&-css

---

**BEM** stands for Block Element Modifier and is a naming convention used to standardise the naming of components across our sites. BEM is quite subjective but at a high level we are looking for:

-   Block: The root of the component.
-   Element: A component part of the Block.
-   Modifier: A variant or extension of the Block.

### Don't

```
.site-search {}
.site-search label {}
.site-search input {}
```

### Do

```
.site-search {}
.site-search__title {}
.site-search__field {}
```

-   Elements may change and no longer be a label or input, breaking the CSS, causing maintenance issues so always select with a class
-   With decent BEM naming you shouldn't need to comment your CSS to say what things are or where they sit
-   Different levels of specificity when nesting

Let's say you have a button inside a header component which inherits a lot of generic button styles but is also unique to the header. This could be done in many ways:

```
// modified inside the header - BAD
.header__button {}

// Or
.header .button {}
```

```
// modifier set on button - OK
.button {}

.button--header {}
```

```
// Generic, reusable modifier despite it being unique to the header - BETTER
.button {}

.button--modifier {}
```

### Exceptions

Naming is hard...document it if it doesn't make sense

## Bad BEM

### Don't

```
<div class="bad"></div>
<div class="bad--modifier"></div>
```

### Do - Modifiers need to extend the base class

```
<div class="good good--modifier"></div>
```

### Don't

```
<ul class="list">
  <li class="list__item">
    <a class="list__item__title" href="#">Item 1</a>
  </li>
</ul>
```

### Do - classes do not reflect the full paper-trail of the DOM.

```
<ul class="list">
  <li class="list__item">
    <a class="list__item-title" href="#">Item 1</a>
  </li>
</ul>

// Or break into a new block

<ul class="list">
  <li class="list-item">
    <a class="list-item__title" href="#">Item 1</a>
  </li>
</ul>
```

## BEM modifiers

We can write modifiers in a few ways, but this is the recommended way:

```
.block {
  &__element {}

  &--modifier .block__element {}
}

// Or

.block {
  &__element {}

  &#{&}--modifier .block__element {}
}
```

-   Here we have a block and element where we will want to modify the elements styles. Following our [property order](#property-order) rules the modifier should be at the bottom.
-   Using `&#{&}--` adds a level of specificity to the declaration but this is not needed if properties are ordered correctly.

## Utility classes

---

### WIP - This section requires discussion/workshops

https://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/

---

Utility classes are single responsibility rules for a specific task. They should not be overwritten.

### Don't

```
.red {
  color: red;
}
```

### Do

```
.u-highlight {
  color: red
}
```

-   These are highly specific rules and should be used sparingly.
-   Due to their targeted rules you could use `!important`
-   Utilities should be abstracted, reusable and not at risk of becoming out of date (EG `.red` may change to `.blue` but `.u-highlight` will likely remain the same).

## Stateful Namespaces

```
.is-open {}
.is-expanded {}
.is-active {}
```

When dealing with an interactive element use stateful namespaces. This could be a `BEM-modifier` but if it is only a temporary state it is not really a modifier.

-   States are very temporary
-   Ensure that States are easily noticed and understood in our HTML

## Javascript

As a general rule, it is a bad idea to bind styles and javascript hooks in your HTML. This is because you cannot remove one without affecting the other and by looking at the HTML you may not even be aware of the connection.

### Don't

```
// Don't use CSS classes to bind JS hooks
<a class="foo" href="#">Trigger JS (class)</a>

const triggerJS_class = document.querySelectorAll(".foo");

if (triggerJS_class) {
  triggerJS_class.forEach((item) => {
    item.addEventListener("click", () => {
      console.log("class worked");
    });
  });
}

// Don't use IDs
<a class="bar" id="foo" href="#">Trigger JS (class)</a>

const triggerJS_id = document.querySelectorAll("#foo");

if (triggerJS_id) {
  triggerJS_id.forEach((item) => {
    item.addEventListener("click", () => {
      console.log("id worked");
    });
  });
}
```

### Do

```
// Use `js-` prefixed custom attributes
<a class="foo" href="#" js-click="trigger-js">Trigger JS</a>

const triggerJS_attribute = document.querySelectorAll('[js-click="trigger-js"]');

if (triggerJS_attribute) {
  triggerJS_attribute.forEach((item) => {
    item.addEventListener("click", () => {
      console.log("attribute worked");
    });
  });
}
```

-   Separate styles from hooks
-   Don't use IDs (not reusable)
-   Easily identify JS functionality in HTML

A common practice is to use `data-` attributes as JS hooks but these are meant to store data. So try to avoid using them.

**NOTE - the use of `.js-` classes is also fine. Just don't bind any styles to them**
