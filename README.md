# Why are coding standards `!important`?

## To deliver high quality products we need to make sure that:

- Everyone in the FE team (and anyone else writing markup/styling) know what is good and bad coding practice
- That we follow a common set of coding conventions and techniques so our projects are structured consistently
- Through the process of agreed standards we are able to make the implementation process more efficient
- Build and maintain products for a reasonable length of time
- We are better able to have different developers working on one project seamlessly
- We avoid situations like frontend rewrites and “do-overs” when implementing our builds
- We stop using out-dated approaches and making common mistakes

# Basic HTML Tag Structuring

- Use appropriate HTML5 tag for intended purpose (instead of using `<div>` for everything) as it is good for SEO and clarity
- Do not use tags like `<p>` or `<br>` to create space between elements as this is an ancient, cumbersome technique that hampers CSS use
- When using large amounts of `<div>` tags, it is helpful to add a closing comment after each `</div>` to make each layer clear where it closes
- Fix broken tag nesting wherever you see it (usually Visual Studio auto-formatting is at fault here)
- Do not add redundant tag nesting or fix it where you encounter it (make sure there is no linked JS functions first!)
- Do not use ALL CAPS in HTML text, because it cannot be transformed to capitalised lowercase text via CSS, only to all lowercase
- Please don’t use inline styles because it’s lazy and hugely problematic when trying to create responsive UIs and using states

## Dont

```
<div>
  <div>
    <div class=“site-header” style=“float:left; padding:20px;”>
      <div class=“page-limits”>
        <h1>SITE NAME</h1>
        <img src=“ /images/logo.png” class=“logo” />
      </div>
    </div>
  </div>
</div>
<div class=“nav”>
  <ul>
    <li><a href=“/link-addr1”>
        <h3>Link 1</h3>
      </a></li>
    <li><a href=“/link-addr2”>
        <h3>Link 2</h3>
      </a></li>
    <li><a href=“/link-addr3”>
        <h3>Link 3</h3>
      </a></li>
  </ul>
</div>
```

## Do

```
<header class=“site-header”>
  <div class=“page-limits”>

    <h1>Site Name</h1>
    <img src=“ /images/logo.png” class=“logo” />

  </div> <!-- /.page-limits -->
</header>

<nav class=“main-nav”>
  <a href=“/link-addr1”>Link 1</a>
  <a href=“/link-addr2”>Link 2</a>
  <a href=“/link-addr3”>Link 3</a>
</nav>
```

# Razor HTML Helpers – Extremely Useful

- When using Razor in a .NET environment, use HTML helpers to avoid repeating blocks of code
- Feed necessary parameters into the helper for the dynamic content

```
@{
    var homeNode = CurrentPage.AncestorOrSelf("home");
    var homeStrong = Model.Content.AncestorsOrSelf().Where(x => x.DocumentTypeAlias == "home").First();
}

@* MOBILE NAV MENU *@
<nav class="mobile-nav">
    <ul class="nav__list">
      @if (homeNode.HasValue("mainNavigation")){
        var treePickerNodes = homeStrong.GetPropertyValue<IEnumerable<IPublishedContent>>("mainNavigation");
        foreach (var item in treePickerNodes)
        {
          var node = Umbraco.Content(Convert.ToInt32(item.Id));
          if(Umbraco.MemberHasAccess(node.Id, node.Path) && node.IsVisible())
          {
            @RenderItem(node);
          }
        }
      }
    </ul>
</nav>

@* NAV ITEM HELPER *@
@helper RenderItem(dynamic node)
{
    var current = node.Id == CurrentPage.Id ? "nav__link--current" : null;
    var homeNode = node.DocumentTypeAlias == "home" ? "Home" : node.Name;
    <li class="nav__item">
        <a href="@node.Url" title="@homeNode" class="nav__link @current">@homeNode</a>
    </li>
}

```

# CSS – Rule Set Structure

## Example Rule Sets

## Traditional

```
.foo,
.bar {
  background-color: green;
  color: red;
  display: block;
}
```

Traditional format has:

- Each selector on its own line
- Each declaration on its own line
- Properties and values on each line ordered A-Z
- Each declaration indented by two spaces
- Closing bracket on its own line
- Standard format used universally and by auto formatters

## Condensed

```
.foo,
.bar {display:block; background-color:green; color:red;}
```

Condensed format has:

- Selector on same line as properties (unless sharing with comma)
- Each declaration on same line
- Properties and values ordered by frequency of use
- Common space between selectors and properties
- Selectors with lots of properties can have 2 or 3 lines
- Blocks are easy to organise and contextualise

# CSS – Don’t Repeat Yourself (DRY)

## Don’t

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

## Do

```
.btn {
  background-color: #fff;
  border: 1px solid #000;
  color: #000;
}
```

- Keep things DRY, aim to use standard classes so you can re-use your code
- SASS includes and extends can encourage poor coding practice, just because it's one line in your code doesn't mean it is when exported
- If you're applying the same include or styles on lots of selectors, create a class and apply it in HTML to keep things DRY
- This is where defined h1, h2, p etc. in Photoshop/XD files would be really useful so we can establish reusable styles

## Exceptions

Sometimes it's still better to KISS (Keep It Simple, Stupid) than DRY.

# CSS – Styling With The ID Attribute

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

- Avoid using IDs for styling as much as possible
- They're overly specific and trump classes in the order of CSS rendering

## Exceptions

Sometimes you may have to use ID because you're working with apps or code you can't otherwise edit. In this case, and only as a last resort, use IDs. (see examples above)

# CSS – Using !important

## Don't

```
.foo {
  color: red !important;
}
```

## Do

```
// Doubling Specificity
.foo.foo {
  // This style will override `.foo` styles
}
```

- Don’t use `!important` if it can be avoided
- Add inline comments above the property explaining why if you have to use `!important`
- Double classes work because it's more specific than just the class name once, but won't override inline styles
- Consider using BEM modifiers where possible. Document any oddities with inline comments

## Exceptions

- Sometimes using 3rd party code/libraries you have to override their specific styles using `!important`. However consider doubling specificity (see example below)
- Utility classes are single responsibility and very specific so could use `!important`

# CSS – Longhand vs Shorthand Properties

## Don't

```
.foo {
  background: url("/path/to/image.jpg") no-repeat 50% 50% #fff;
  font: 2rem/1.6 "Helvetica", sans-serif;
}
```

## Do

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

- Avoid shorthand for background and font properties
- For these properties the non-described properties automatically are set to none/default/0 which causes issues
- The code is more verbose when making responsive changes

## Non-described properties

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

## Exceptions

Feel free to use shorthand properties for `margin`, `padding`, `border`, and `transform`.

# CSS – Formatting

## Do

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

  &::before {
    ...
  }

  &:hover {
    ...
  }
}
```

- Include a doc-block/intro-block if necessary to explain anything not self documenting or hacky
- Wrap calculations in brackets for readability (`left: calc(100% - (#{$gutter-width} * 2));`)
- Put spaces between the values and the mathematical operators
- Put multiplying and dividing values after the starting value
- Use lowercase for selectors and properties
- Use `//` for inline commenting not the block level `/* */`
- Inline comments should start on a new line preceding the property/issue they're describing
- Use sentence case for your comments
- Do not end with a full stop
- Specify units on zero duration times
- Don't specify units on zero length values (eg `0px` should be `0`)
- Add a leading zero for decimal places
- Don't go to more than three decimal places, the fewer the better (some browsers do not read more than 2 decimal places anyway)
- Include parentheses on mixins regardless if there are no arguments
- Pseudo-elements should use ‘::‘(after, before, first-letter, first-line, marker, placeholder, and selection)
- Pseudo-selectors should use ‘:’ (hover, focus, active, nth-child etc.)

# CSS – “Magic Numbers”

## Don't

```
.foo {
  left: 23px;
}
```

## Do

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

- Avoid using "magic numbers" that "just work“ as they may become problematic with future reskins or other changes
- Use calculations based on existing values (or variables)

## Exceptions

If you have to set a value then add a comment explaining why this number "just works"

# CSS – Using Variables

When first setting up your project you should go through and define a series of variables to help with the maintenance of the project, it's a lot easier to change the property of one variable rather than hunting for all appearances of, say, a font-family.

Properties that typically benefit from variables are:

- border-radius/ border-width
- color (font, backgrounds, borders)
- font properties (family, size, weight)
- margin/padding (gutters, grid gutters)
- transforms
- transition (duration, easing)

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

- Variables should be defined in a single file/folder
- Variable names should be descriptive
- A well implemented variables structure can make site theming very easy to manage, modify and iterate

**NOTE - maybe look into defining [global variables](https://github.com/we-make-websites/wmw-coding-guidelines/blob/master/css/README.md#variable-naming)**

# Colours

## Don't

```
// Don't use system colours
$red: RED;

// Don't use uppercase HEX
$red: #FF0000;

// Don't use hsl (difficult to read)
$red: hsl(0, 100%, 50%);
```

## Do

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

- Colours should be named using the [name that color](https://marketplace.visualstudio.com/items?itemName=guillaumedoutriaux.name-that-color) VS code extension or [website](https://chir.ag/projects/name-that-color/#6195ED) (unless they're named in the client's brand guidelines)
- If you have two or more colours similar enough to share the same name then suffix them with a number where the 1 is the darkest variant of the colour
- HEX colours should be written in lowercase as it is easier to read

**NOTE - if using rgba colours maybe use original colour name EG `$black-rgba-5: rgba($black, .5)`**

# CSS – BEM (Block Element Modifier)

**BEM** stands for Block Element Modifier and is a naming convention used to standardise the naming of components across our sites. BEM is quite subjective but at a high level we are looking for:

- Block: The root of the component.
- Element: A component part of the Block.
- Modifier: A variant or extension of the Block.

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

- Elements may change and no longer be a label or input, breaking the CSS, causing maintenance issues so always select with a class
- With decent BEM naming you shouldn't need to comment your CSS to say what things are or where they sit
- Different levels of specificity when nesting

Let's say you have a button inside a header component which inherits a lot of generic button styles but is also unique to the header. This could be done in many ways:

```
// modified inside the header - BAD
.header__button {}

// Or
.header .button {}
```

- Unique class will mean we have to create a whole new set of styles for it
- Reduces our ability to update the site theme easily and quickly
- Can quickly make the CSS unmanageable if this practice continues across the project

```
// modifier set on button - OK
.button {}

.button--header {}
```

- Uses common block & modifier classes so styles will be inherited
- Downside is the modifier is still specific to the header so styles will not be common to other areas of the layout

```
// Generic, reusable modifier despite it being unique to the header - BETTER
.button {}

.button--modifier {}
```

- Uses common block & modifier classes so styles will be inherited
- Modifier class is generic so styles will be reused across different areas

## Exceptions

Naming is hard...document it if it doesn't make sense

# Bad BEM

## Don't

```
<div class="bad"></div>
<div class="bad--modifier"></div>
```

## Do - Modifiers need to extend the base class

```
<div class="good good--modifier"></div>
```

## Don't

```
<ul class="list">
  <li class="list__item">
    <a class="list__item__title" href="#">Item 1</a>
  </li>
</ul>
```

## Do - classes do not reflect the full paper-trail of the DOM.

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

- Here we have a block and element where we will want to modify the elements styles
- Using `&#{&}--` adds a level of specificity to the declaration but this is not needed if properties are ordered correctly

# CSS – Utility Classes

Utility classes are single responsibility rules for a specific task. They should not be overwritten.

### Don't

```
// We should not use class names which may later become incorrect if there is a change to the styling theme
.red {
  color: red;
}
```

### Do

```
// The purpose of the class will not change, unlike its visual appearance
.u-highlight {
  color: red
}
```

- These are highly specific rules and should be used sparingly.
- Due to their targeted rules you could use `!important`
- Utilities should be abstracted, reusable and not at risk of becoming out of date (EG `.red` may change to `.blue` but `.u-highlight` will likely remain the same).

# CSS - Stateful Namespaces

```
.is-open {}
.is-expanded {}
.is-active {}
.is-visible {}
```

## Don’t

```
// Element expanded styles will apply to any ancestors that have the class
.accordion .is-expanded {}
```

## Do

```
// Styling rules are applied to the target element only
.accordion-item.is-expanded {}

// or (preferable)
.accordion-item {
  &.is-expanded   {}
}
```

When dealing with an interactive element use stateful namespaces. This could be a `BEM-modifier` but if it is only a temporary state it is not really a modifier.

- States are very temporary
- Ensure that States are easily noticed and understood in our HTML
- Makes sure element states do not conflict with ancestor/descendent elements using the same class

# JavaScript – Hook Attributes

## Don’t

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

## Do

```
// Use `js-` prefixed custom attributes
<a class="foo" href="#" js-behaviour="trigger-js">Trigger JS</a>

const triggerJS_attribute = document.querySelectorAll('[js-behaviour="trigger-js"]');

if (triggerJS_attribute) {
  triggerJS_attribute.forEach((item) => {
    item.addEventListener("click", () => {
      console.log("attribute worked");
    });
  });
}
```

## jQuery examples

```
// <a href="#" js-behaviour="open-model">Open modal</a>
$('[js-behaviour="open-model"]').on("click", function (e) {
  e.preventDefault();
  console.log("Fire open model");
});

// <a href="#" js-behaviour="close-model">Close modal</a>
$('[js-behaviour="close-model"]').on("click", function (e) {
  e.preventDefault();
  console.log("Fire close model");
});

// <a href="#" js-behaviour="get-data-information" data-information="This is some information stored as an attribute">Data</a>
$('[js-behaviour="get-data-information"]').on("click", function (e) {
  e.preventDefault();
  console.log($(this).data("information"));
});
```

- Separate styles from hooks
- Don't use IDs (not reusable)
- Easily identify JS functionality in HTML
- A common practice is to use data- attributes as JS hooks but these are meant to store data. So try to avoid using them.
- The use of .js- classes is also fine. Just don't bind any styles to them

# General Front-end Tips - Do's

- Structure code neatly and consistently.
- Use comments to annotate and structure blocks of code.
- Delete cruft (useless or obsolete code) or at least leave a comment for future deletion when safe to do so (including your name & date for reference).
- Make use of HTML5 tags such as `<address>`, `<time>`, `<code>`, `<mark>` etc instead of using `<div>`, `<p>` and `<span>` for everything.
- Keep up with new CSS3 or HTML5 features then use them when they are supported (check caniuse.com).
- Use new layout methods such as Flexbox/CSS Grid rather than older methods like float and inline-block wherever possible as they have a finer level of control.
- Split CSS/SASS/LESS code up into separate contextual files and `@include` them all into a master file. This makes it easier to find selector groups.
- Use a CSS pre-processor, such as SASS (preferably) or Less, to take advantage of very useful features like mixins, variables & functions.
- Use a task runner or VS plugin such as Web Compiler, Gulp/Grunt/Webpack to minify or combine JS, CSS and other script files on save to enhance site performance
- Use the `modernizr.js` plugin to automatically check that the CSS/HTML features your site uses are supported in the browser the user is viewing the site on. If not, graceful fall-backs can then be set via CSS.
- Use layout & styling frameworks (bootstrap, grids) sparingly if they are necessary at all as they can result in a bloated, slow and overly-complex frontend
- Use tables for displaying tabular data or displaying grids.
- When animating with CSS, use the Z (translate3d) property to trigger 3D hardware acceleration. This speeds up animations and makes them transition more fluidly. Even setting it to 0 will activate 3D hardware, so it’s a good trick to use (especially for mobile).
- If you change another developers CSS or JS in a project they are leading, drop a comment saying why the change was necessary and initial/date it. This stops the code being reverted back mistakenly or bugs being reintroduced post-fix.
- Use the `::before` & `::after` pseudo-classes to reduce unnecessary HTML tag use (can be used for layout as well as text). NOTE - we must consider accessibility when using content in pseudo elements
- Use SVG’s for non-image graphics (logos, icons, infographics) as they are far more versatile and smaller in file-size. It’s quite easy to convert `.png` or `.jpg` into an SVG using Adobe Illustrator or create custom font-glyph sets using Iconmoon.io.

# General Front-end Tips - Don't

- Produce messy, cruft-ridden, inconsistent or uncommented code in projects (confuses other developers and slows down development).
- Use inline styles as they are high-priority and problematic for responsive designs (unless it’s an email template). Inline styles should only be injected dynamically by JavaScript in most circumstances (and even that should be avoided).
- Use out-dated tags, attributes or techniques (anything marked as deprecated on w3schools.com) Deprecated tag Examples: `<font>`, `<frameset>`, `<strike>`, `<big>`
- Use the CSS property float on elements that do not require it, as it can cause severe layout issues when used incorrectly. Float should rarely be used these days anyway as Flexbox/CSS Grid handles most layout scenarios far better
- Use tables for general page layouts (unless an HTML email template)
- Use JQuery animation methods like `.fadeIn()`, `.slideToggle()` etc. This is because they are very resource intensive and slow your site down (especially on mobile devices). Use CSS transitions & transforms instead which are now pretty much 100% browser & device supported (sometimes prefixes are required – use SASS mixins or task-runners for that). They are smoother and far less resource hungry (especially when used with the 3d ‘z’ axis)
- Use old projects as a base for new ones as it results in a huge amount of cruft which has unknown consequences if removed. Always import a fresh frontend project template into a new project. This way you know what effect every block of CSS and JS is having on the layout.
- Only use a vanilla CSS stylesheet without a pre-processor (SASS/LESS). For some it may be tempting to save time at the beginning, but in the long term it results in messy, spaghetti CSS with a hard to navigate structure (especially for other developers coming on to the project in the future). It’s also extremely difficult to create themes without a pre-processor.
- Use `.jpg` image format for graphics. `.jpg` should only be used for photo-imagery wherever possible.
