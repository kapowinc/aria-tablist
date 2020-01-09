# Aria Tablist

[![npm version](https://img.shields.io/npm/v/aria-tablist.svg)](http://npm.im/aria-tablist)
[![gzip size](http://img.badgesize.io/https://unpkg.com/aria-tablist/dist/aria-tablist.min.js?compression=gzip)](https://unpkg.com/aria-tablist/dist/aria-tablist.min.js)

Dependency-free plain JavaScript module for WCAG compliant tablists

[Try out the examples](https://mynamesleon.github.io/aria-tablist/examples/)

Key design goals and features are:

-   **multi and single select modes**
-   **horizontal and vertical modes**: Adjusts arrow key usage for moving focus between tabs
-   **progressive enhancement**: Allows for only the tab and panel relationship to be indicated in the DOM, and adds `role` and `aria` attributes automatically as needed
-   **accessibility**: Follows the WCAG spec
-   **compatibility**: Broad browser and device support (IE9+)
-   **starting states**: Can use `aria-selected="true"` to indicate which tab(s) should be enabled by default.
-   **deletion**: Can enable tab (and panel) deletion

## Installation / usage

Grab from NPM and use in a module system:

```
npm install aria-tablist
```

```javascript
import AriaTablist from 'aria-tablist';
new AriaTablist(document.getElementById('tablist'));
```

Or grab the minified JavaScript from unpkg:

```html
<script src="https://unpkg.com/aria-tablist/dist/aria-tablist.min.js"></script>
```

## HTML Requirements / Progressive Enhancement

When the module is called on an element, the following steps are taken:

1. The module will look for elements with `role="tab"` set
2. If none are found, all direct children will be processed
3. For each tab (assumed or otherwise) the module will check for a matching panel by:
    1. Checking for an `aria-controls` attribute on the tab, and searching for an element with a matching ID
    2. If the tab has an `id`, searching for an element with an `aria-labelledby` attribute that matches that `id`
4. For any tabs without a matching panel, any applicable ARIA attributes will be removed from it
5. For the tabs that have associated panels, all relevant ARIA attributes will be set automatically

This means your HTML only needs to indicate the relationship between the tabs and panels, and the module will handle the rest:

```html
<div id="tabs">
    <div id="tab-1">Panel 1</div>
    <div id="tab-2">Panel 2</div>
    <div id="tab-3">Panel 3</div>
</div>

<div aria-labelledby="tab-1">...</div>
<div aria-labelledby="tab-2">...</div>
<div aria-labelledby="tab-3">...</div>
```

So if you need to cater for users without JavaScript, or if the JavaScript fails to load for whatever reason, there won't be any applicable roles set that would confuse a screen reader user.

You can of course include all of the optimal ARIA attributes straight away if you wish, including indicating which tab should be active by default:

```html
<div id="tabs" role="tablist" aria-label="Tabs">
    <div role="tab" tabindex="-1" aria-controls="panel-1" id="tab-1">
        Panel 1
    </div>
    <div role="tab" tabindex="0" aria-selected="true" aria-controls="panel-2" id="tab-2">
        Panel 2
    </div>
    <div role="tab" tabindex="-1" aria-controls="panel-3" id="tab-3">
        Panel 3
    </div>
</div>

<div role="tabpanel" aria-labelledby="tab-1" hidden="hidden" id="panel-1">...</div>
<div role="tabpanel" aria-labelledby="tab-2" id="panel-2">...</div>
<div role="tabpanel" aria-labelledby="tab-3" hidden="hidden" id="panel-3">...</div>
```

## Options

Most of the functionality is assumed from the included ARIA attributes in your HTML (see the [examples](https://mynamesleon.github.io/aria-tablist/examples/)), but some basic options are also available:

```javascript
{
    /**
     * @description delay in milliseconds before showing tab(s) from user interaction
     */
    delay: 0,

    /**
     * @description allow tab deletion - can be overridden per tab by setting data-deletable="false"
     */
    deletable: false,

    /**
     * @description callback each time a tab opens
     */
    onOpen: (panel) => {},

    /**
     * @description callback each time a tab closes
     */
    onClose: (panel) => {},

    /**
     * @description callback when a tab is deleted
     */
    onDelete: (tab) => {},

    /**
     * @description callback once ready
     */
    onReady: (tablist) => {}
}
```

All component options that accept a Function will have their context (`this`) set to include the full autocomplete API (assuming you use a normal `function: () {}` declaration for the callbacks instead of arrow functions). 

## API

The returned `AriaTablist` class instance exposes the following API (which is also available on the original element's `ariaTablist` property):

```typescript
{
    /**
     * @description the tab elements the module currently recognises
     */
    tabs: Element[];

    /**
     * @description the panel elements the module currently recognises
     */
    panels: Element[];

    /**
     * @description the current options object
     */
    options: Object;

    /**
     * @description trigger a particular tab to open
     * @param {Number|Element} index: tab index, or tab element
     * @param {Boolean=} focusTab: whether to move focus to the tab
     */
    open(index: Number|Element, focusTab: Boolean = true): void;

    /**
     * @description trigger a particular tab to close
     * @param {Number|Element} index: tab index, or tab element
     */
    close(index: Number|Element): void;

    /**
     * @description delete a particular tab and its corresponding panel (if deletable)
     * @param {Number|Element} index: tab index, or tab element
     */
    delete(index: Number|Element): void;

    /**
     * @description destroy the module - does not remove the elements from the DOM
     */
    destroy(): void;
}
```
