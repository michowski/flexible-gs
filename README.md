# Flexible grid system
Responsive grid system based on Flexbox and built with SCSS. Actually: an ultra light library for creating responsive websites. Fully customizable with SCSS. Reasonable alternative to complex frameworks.

The best way to understand Flexible possibilities is to see source code of `demo/demo.html` and `demo/demo.scss` files. You can watch live demo [here](http://www.michowski.com/dev/flexible-gs/demo/demo.html).
## Grid system
### TL;DR cheatsheet
Classes:
* `row` - a row - it's meant to wrap columns (they won't work properly if not within a row element)
* `<breakpoint>-col` - generic column, not growing to the size of row [opposite to `col-auto`]
* `<breakpoint>-col-<x>` - regular column, taking `<x>` units (parts) of row width
* `<breakpoint>-col-auto` - column automically growing to the size of row [opposite to `col`]
* `<breakpoint>-offset-<x>` - column offset equal to `<x>` units (parts) of row width
* `<breakpoint>-<feature-name>` - this class adds a responsive feature to the element

By default the breakpoints are: `xs`, `s`, `m`, `l`, `xl` and you ommit `xs-` for class names (because it's the starting breakpoint). Rows are divided into `12` units. Of course you can change all of that. 

To use breakpoints for own media queries, simply write your code inside `@include flexible-breakpoint( <breakpoint> ) { ... }`. To use reversed functionality (`(max-width: ...)` media query), go with `@include flexible-breakpoint-down( <breakpoint> ) { ... }`.
### Basic usage
Just import the `flexible-gs.scss` file and use `flexible-create` mixin to create your own grid system:
```scss
@import "flexible-gs";

// Create grid with 12 "units" and 10px horizontal gutter
@include flexible-create( 12, 10px );
```
Voila! Your grid system is ready to use. Now you can write some HTML:
```html
<div class="row">
  <div class="col-12 m-col-8"></div>
  <div class="col-12 m-col-4"></div>
</div>
```
The code above creates row with 2 columns inside. Their widths are equal to 8/12 and 4/12 of row width on screens wider than `m` size (by default: `48em`). On thinner screens columns are filling the whole row (12/12 of its width).

As you can already guess, the syntax for column class is `<breakpoint>-col-<units>`. We don't need to write the breakpoint part for the starting breakpoint (here: `xs`), as its equal to 0, but it can be changed by passing special argument to `flexible-create`.

These are all arguments you can pass to `flexible-create` mixin:
```scss
@mixin flexible-create(
  // Number of parts we divide row into
  $unit-number: 12,
  // Horizontal gutter (column padding)
  $gutter-x: 0,
  // Vertical gutter (column padding)
  $gutter-y: 0,
  // Whether to generate classes named ".col-3" (true) or ".col-3-12" (false)
  // [this column width is equal to 3/12 of row width]
  $simple-class-name: true,
  // Whether to add first breakpoint prefix (i.e. xs-feature) to classes (true) or not (false)
  $first-breakpoint-prefix: false )
```  
### Column offset
You can simply add offset to the column using `<breakpoint>-offset-<units>` syntax, i.e:
```html
<div class="col-3 m-offset-3"></div>
```
The width of above column is equal to 3/12 of row. On screens wider than `m` it actually takes 6/12 of a row width, because we have added offset of 3 units.
### Custom columns order
If you want to reverse order of columns within row, add `row_reverse` class to the row. Remember that both rows and columns are `flexbox` elements. This means you can change the order of columns using CSS `order` property.
### Auto width column
If you want your column to auto fill the row (even if there are already other columns inside the row), simply write:
```HTML
<div class="col-auto"></div>
```
When there are both regular columns and `col-auto` columns inside one row, the latter ones have all equal width and fill the space not taken by regular columns (by regular we mean those with fixed width, not auto).
### Irregular columns aka generic columns
Sometimes you may want to make column width independent of grid system:
```HTML
<div class="col my-custom-column"></div>
```
You can attach any properties to `.my-custom-column` in CSS, like fixed width, etc. By default, `<breakpoint>-col` (here: `col`) is just a generic column, which doesn't grow to row size.
## Breakpoints
By default, Flexible uses same breakpoints as Bootstrap (except `lg` is simply `l`, `md` is `m` and `lg` is `l`). To use them in your SCSS code, you can use `flexible-breakpoint` mixin:
```scss
body {
  font-size: 80%;
  @include flexible-breakpoint(s) {
    font-size: 90%;
  }
  @include flexible-breakpoint(xs) {
    font-size: 100%;
  }
}
```
This makes font size of `body` becoming larger for wider screens. If you want to add reversed functionality, you can use `flexible-breakpoint-down` mixin:
```scss
body {
  font-size: 100%;
  @include flexible-breakpoint-down(m) {
    font-size: 90%;
  }
  @include flexible-breakpoint-down(s) {
    font-size: 80%;
  }
}
```
This makes font size of `body` becoming smaller for thinner screens.
### Custom breakpoints
You are free to override breakpoints before calling `flexible-create`:
```scss
// Custom breakpoint map
$flexible-breakpoint-map: (
  all: 0,
  small: 30em,
  big: 50em
);
```
We use the "up breakpoints" convention, as it follows the "mobile-first design" pattern. That means that **you have to** declare the breakpoint with width equal to 0. That doesn't mean you have to use its name. However, if you prefer to do so, simply set 5th argument of `flexible-create` to true.
## Responsive features
By default, Flexible comes with some helpers classes, which allows us to quickly attach responsive features to our elements. For example:
```html
<p class="align-left s-align-center m-align-right">Some text</p>
```
The `text-align` property of the element above changes with screen width. Its value is `left` for devices below `s` size, `center` for devices above `s` size and below `m` size, and `right` for devices above `m` size.

If you find those features useless, you can simply get rid of them before `flexible-create`:
```scss
$flexible-default-features: ();
```
### Custom responsive features
If you want to add your own helpers classes, simply set the value of `flexible-features` variable before using `flexible-create`:
```scss
$flexible-features: (
  big-text: (font-size: 200%)
);
```
Now mixin `flexible-create` generates additional classes for our feature. For default breakpoints they are as follows: `big-text`, `s-big-text`, `m-big-text`, `l-big-text` and `xl-big-text`.

You can add multiple features with multiple properties and values, i.e:
```scss
$flexible-features: (
  big-text: (font-size: 200%),
  cool-text: (font-size: 80%, text-decoration: underline, text-align: center)
);
```
