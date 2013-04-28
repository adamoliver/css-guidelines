# CSS Guidelines

---

The goal of this document is to outline guidelines to keep stylesheets maintainable, keep code readable and allow several developers working on the same project to work in a unified way.

## Contents

* [CSS Document Anatomy](#css-document-anatomy)
* [Source order](#source-order)
* [Ruleset structure](#ruleset-structure)
* [Naming conventions](#naming-conventions)
* [Comments](#comments)
* [Writing CSS](#writing-css)
* [Shorthand](#shorthand)
* [IDs](#ids)
* [Selectors](#selectors)
* [CSS selector intent](#css-selector-indent)
* [!important](#important)
* [Magic numbers and absolutes](#magic-numbers-and-absolutes)
* [Conditional stylesheets](#conditional-stylesheets)
* [Debugging](#debugging)

---

## CSS Document Anatomy

When working with multiple developers on a project it is important to keep the structure unified.

### General

Use 4 space indents over tabs and write multi-line CSS/LESS

### Use one base CSS/LESS file, a file for an individual app, and a file for any unique styles on a page

### Table of contents

At the top of stylesheets, create and maintain a table of contents which will detail the sections contained in the document, for example:

    /*------------------------------------*\
        Table of contents
    \*------------------------------------*/
    /**
     * CONTENTS............You’re reading it!
     * RESET...............Set our reset defaults
     * FONT-FACE...........Import brand font files
     */


### Section titles

For each section in the table of contents, create a corresponding section title so that other developers can easily skip to that section.


    /*------------------------------------*\
        RESET
    \*------------------------------------*/



## Source order

Try and write stylesheets in specificity order. This ensures that you take full advantage of inheritance and CSS’ first &lt;i&gt;C&lt;/i&gt;; the cascade.

A well ordered stylesheet will be ordered something like this:

1. **Reset** – ground zero.
2. **Elements** – unclassed `h1`, unclassed `ul` etc.
3. **Objects and abstractions** — generic, underlying design patterns.
4. **Components** – full components constructed from objects and their
   extensions.
5. **Style trumps** – error states etc.
 

## Ruleset structure

* Use hyphen delimited class names
* 4 space indented
* Multi-line
* Declarations in relevance (NOT alphabetical) order
* Indent vendor prefixed declarations so that their values are aligned
* Always include the final semi-colon in a ruleset

An example (CSS):

.search {
    color: #333;
    padding: 3px 10px;
}

.search input {
    border: 1px solid #ccc;
    background-color: #fff;
    -webkit-border-radius: 5px;
       -moz-border-radius: 5px;
            border-radius: 5px;
}


An example (LESS):

.search {
    color: #333;
    padding: 3px 10px;

    .search input {
        padding: 3px
        border: 1px solid #ccc;
        background-color: #fff;
        .border-radius(5px); // Uses a mixin for vendor prefixes
    }
}


## Naming conventions

Generally, use hyphen delimited classes (e.g. '.foo-bar' rather than '.foo_bar' or '.fooBar'.

The exception to this may come from 3rd party plugins which do not follow this convention.


## Comments

You should document and comment our code as much as you possibly can, what may
seem or feel transparent and self explanatory to you may not be to another dev.


#### Qualified selectors

You should not prepended a selection with an element because:

* They totally inhibit reusability on another element.
* They increase specificity.
* They increase browser workload (decreasing performance).

For example, you should not write 'ul.nav{}' if you can just have '.nav'. Using the prior means that you would have to write another selector to be able to use 'ol.nav'.

Of course sometimes you will _want_ to
qualify a class with an element (e.g. if you have a generic `.error` class that needs to look different when applied to different elements (e.g.
`.error{ color:#ff0000; }` `div.error{ padding:15px; }`)), but generally avoid it where possible.


### Quasi-qualifying selectors

It is acceptable to use quasi-qalifying selectors (i.e. commenting out the leading type selector) to communicate where the selector is intended to be used.

For example:

    /*ol*/.breadcrumb{}
    /*p*/.intro{}
    /*ul*/.image-thumbs{}

---

## Writing CSS

The previous section dealt with how we structure and form our CSS; they were
very quantifiable rules. The next section is a little more theoretical and deals with attitude and approach.

### Building new components

When building a new component write markup **before** CSS. This means you can
visually see which CSS properties are naturally inherited and thus avoid
reapplying redundant styles.

By writing markup first you can focus on data, content and semantics and then
apply only the relevant classes and CSS _afterwards_.


### Layout

A grid system like 960gs or Twitter Bootstrap should be used for layout. Using a grid makes the front end a lot more adaptable and quick to work with.

You should never apply any styles to a grid item, they are for layout purposes only. Apply styling to content _inside_ a grid item. Never, under _any_ circumstances, apply box-model properties to a grid item.


## Shorthand

**Shorthand CSS needs to be used with caution.**

Generally, use shorthand when setting more than one attribute. For example, 
use 'background-color:#ff0000;' if you only need to set the background colour. Use 'background: #ff0000 url(image.jpg) 0 0 no-repeat; if you need to set more than one attribute.

Be explicit in which properties you set and take care to not inadvertently unset others with shorthand. E.g. if you only want to remove the bottom margin on an element then there is no sense in setting all margins to zero with `margin:0;`.

Shorthand is good, but can easily be misused.

## IDs

A quick note on IDs in CSS before we dive into selectors in general.

**NEVER use IDs in CSS.**

They can be used in your markup for JS and fragment identifiers but use only
classes for styling. You don’t want to see a single ID in any stylesheets!

An ID is **255** times more specific than a class, so never ever use
them in CSS _ever_.

## Selectors

Keep selectors short, efficient and portable.

Heavily location-based selectors are bad for a number of reasons. For example,
take `.sidebar h3 span{}`. This selector is too location-based and thus we
cannot move that `span` outside of a `h3` outside of `.sidebar` and maintain
styling.

Selectors which are too long also introduce performance issues; the more checks
in a selector (e.g. `.sidebar h3 span` has three checks, `.content ul p a` has
four), the more work the browser has to do.

Make sure styles aren’t dependent on location where possible, and make sure
selectors are nice and short.

Selectors as a whole should be kept short (e.g. one class deep) but the class
names themselves should be as long as they need to be. A class of `.user-avatar`
is far nicer than `.usr-avt`.


## CSS selector intent

Instead of using selectors to drill down the DOM to an element, it is often best
to put a class on the element you explicitly want to style. Let’s take a
specific example with a selector like `.header ul{}`…

Let’s imagine that `ul` is indeed the main navigation for our website. It lives
in the header as you might expect and is currently the only `ul` in there;
`.header ul{}` will work, but it’s not ideal or advisable. It’s not very future
proof and certainly not explicit enough. As soon as we add another `ul` to that
header it will adopt the styling of our main nav and the the chances are it
won’t want to. This means we either have to refactor a lot of code _or_ undo a
lot of styling on subsequent `ul`s in that `.header` to remove the effects of
the far reaching selector.

Your selector’s intent must match that of your reason for styling something;
ask yourself **‘am I selecting this because it’s a `ul` inside of `.header` or
because it is my site’s main nav?’**. The answer to this will determine your
selector.


## `!important`

The use of '!important' should only ever be used proactively. That is, it should only be used when you only every want a selector to be styled in a certain way and want to ensure that the style is never overwritten. '!important should never be used reactively when you are using it to get yourself out of a hole.

For example, you may use '.error { colour: #ff0000!important}' as you know you will **always** want this selector to be styled with red text. 


## Magic numbers and absolutes


A magic number is a number which is used because ‘it just works’. These are bad
because they rarely work for any real reason and are not usually very
futureproof or flexible/forgiving. They tend to fix symptoms and not problems.

For example, if you want to position an element at the bottom of it's parent you should never use something like '.more-info { top: 100px; }' just because it works. It would be much better to use '.more-info { top: 100%; } which would ensure that even if the parent element changed in size, the element would still be positioned at the bottom of it's parent.


## Conditional stylesheets

IE stylesheets should only be used as a last resort. They should only really be used to circumvent blatant lack of support, for example PNG fixes or rounded corner styles. Any other use of conditional stylesheets would only ever be used as a temporary measure and should be refactored.


## Debugging

If you run into a CSS problem **take code away before you start adding more** in
a bid to fix it. The problem exists in CSS that is already written, more CSS
isn’t the right answer!

Delete chunks of markup and CSS until your problem goes away, then you can
determine which part of the code the problem lies in.

It can be tempting to put an `overflow:hidden;` on something to hide the effects
of a layout quirk, but overflow was probably never the problem; **fix the
problem, not its symptoms.**

