<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [What is up with focus](#what-is-up-with-focus)
  - [The status quo](#the-status-quo)
    - [`tabindex`](#tabindex)
    - [`:focus`](#focus)
    - [`focus()`](#focus)
    - [`document.activeElement`](#documentactiveelement)
  - [Experimental](#experimental)
    - [`:focus-visible`](#focus-visible)
    - [`inert`](#inert)
    - [Spatial navigation](#spatial-navigation)
  - [Gaps?](#gaps)
    - [Top layer API](#top-layer-api)
    - [Specify a heuristic for matching `:focus-indicator`](#specify-a-heuristic-for-matching-focus-indicator)
    - [Programmatically set the focus navigation start point (needs a better name)](#programmatically-set-the-focus-navigation-start-point-needs-a-better-name)
    - [Determine previous/next focusable item (sequential or spatial)](#determine-previousnext-focusable-item-sequential-or-spatial)
    - [`focusable` property?](#focusable-property)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# What is up with focus

## The status quo

Minimal, labour-intensive methods for managing focus exist:

### `tabindex`

This attribute allows authors to indicate that an element should be either sequentially or programmatically focusable.

**Issues**

- Values greater than zero force the element to the beginning of the sequential focus order.
- "index" is a poor semantic fit for the actual purpose of values less than or greater than zero -
essentially it allows you to state "in focus order", "focusable but not in focus order",
and the default "not focusable".
- There is no way to make an implicitly focusable element such as a `<button>` unfocusable -
it can only be removed from the focus order.
- There is no way to make a custom element implicitly focusable - it must sprout a `tabindex` attribute,
which if removed makes the element unfocusable.
- It's counter-intuitive that it's not in numeric order - values _greater than_ zero jump to the start of the tab order;
elements with `tabindex=0` come after.

**Related gaps/proposals**

- [Programmatically set the focus navigation start point](#programmatically-set-the-focus-navigation-start-point-needs-a-better-name)
- [Determine previous/next focusable item (sequential or spatial)](#determine-previousnext-focusable-item-sequential-or-spatial)


### `:focus`

This selector is matched when an element has focus, and the document also has focus (i.e. `document.activeElement` may not match `:focus`).

**Issues**

- An element having focus doesn't always warrant its having a visible focus style. This is dependent on user and context.
- See `:focus-indicator` below.

**Related gaps/proposals**

- [`:focus-visible`](#focus-ring-aka-focus-indicator-focus-visible)

### `focus()`

This method allows an author to programmatically move focus to a focusable element (i.e. an element with a `tabindex` attribute).

**Issues**

- Used to manage focus, but can only be used on focusable elements, i.e. can't be used to set the focus navigation start point, which is often the desired behaviour.

### `document.activeElement`

This returns the active - i.e. focused - element for `document`.

**Issues**

If the "deep active element" is within Shadow DOM, the shadow host is the document's active element

## Experimental

### `:focus-visible`

_(Was `:focus-ring` and briefly `:focus-indicator`.)_

This pseudo-selector matches when the user agent determines that a focus indicator should be shown.
For example, an unstyled `<button>` element will typically only have a focus ring drawn if it received focus via the keyboard, or received a keyboard event.

**Status**

- In (at least by some name) [Selectors level 4 spec draft](https://github.com/w3c/csswg-drafts/pull/2104).
- [Speculative polyfill](https://github.com/WICG/focus-ring) available.
- Firefox: incomplete implementation.
- Other browsers: not implemented (Planned for Q1 on Chrome).

### `inert`

Allows an author to declare a DOM subtree to be [inert](https://html.spec.whatwg.org/multipage/interaction.html#inert-subtrees).

More detail in the [explainer](https://github.com/WICG/inert/blob/master/README.md).

**Example use cases**

- rendering content, such as a menu, offscreen, to avoid rendering cost during performance-critical interactions or to avoid rendering multiple times;
- a carousel or other type of content cycler (such as a "tweet cycler") which visually hides non-current items by placing them at a lower z-index than the active item, or by setting their opacity to zero, and animates transitions between items;
- "infinitely scrolling" UI which re-uses and/or pre-renders nodes - pre-rendered nodes may be offscreen and made non-inert when they are visible on-screen;
- content which is intended to be non-interactive due to some modal UI showing.

### Spatial navigation

- Repo/explainer https://github.com/lgeweb/spatial-navigation
- BlinkOn presentation https://docs.google.com/presentation/d/1x4RaJIzTYeX0-nySVuq0TThe5shfmOsjbGIMrZJLBTE/edit#slide=id.g2647c2e7b4_2_42
- Draft https://drafts.csswg.org/css-ui-4/#nav-dir
- Discussed in CSSWG, issue filed to move to WICG: https://github.com/w3c/csswg-drafts/issues/1948
- tabindex in CSS? https://github.com/w3c/csswg-drafts/issues/1748
- enyo "Spotlight" https://github.com/enyojs/spotlight
- https://github.com/lgeweb/spatial-navigation/blob/master/explainer.md#user-content-nav-rule-property-cssui4

## Gaps?

### Top layer API

For some (but not all) of the use cases of [inert](#inert), a better fit would be an API which replicates some of the logic of [`requestFullscreen()`](https://fullscreen.spec.whatwg.org/#dom-element-requestfullscreen) and/or [`<dialog>`](https://html.spec.whatwg.org/multipage/interactive-elements.html#the-dialog-element):

- render above all other layers, regardless of `z-index` property
- optionally make all _other_ elements in the document [inert](https://html.spec.whatwg.org/multipage/interaction.html#inert-subtrees).

### Specify a heuristic for matching `:focus-indicator`

Allow authors to specify when an element should match `:focus-indicator`.

### Programmatically set the focus navigation start point (needs a better name)

Allow authors to manage focus by determining the focus navigation start point, instead of setting focus to a container element.

For example, if a keyboard shortcut causes a piece of UI to appear, the author could set the focus navigation start point to its containing element, so that if the user chooses to use sequential focus navigation they would be navigating inside the newly appeared element.

### Determine previous/next focusable item (sequential or spatial)

### `focusable` property?

- Some way to set/get whether an element is focusable.
- Maybe settable for custom elements only?
