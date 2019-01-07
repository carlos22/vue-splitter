# vue-splitter

[![Build Status](https://travis-ci.org/rmp135/vue-splitter.svg?branch=master)](https://travis-ci.org/rmp135/vue-splitter)

For splitting a Vue in two!

__[Demo](https://rmp135.github.io/vue-splitter/)__

## Installing

Install from npm.

`yarn add @rmp135/vue-splitter`

Import the component.

```javascript
import VueSplitter from "@rmp135/vue-splitter"
```

Include it in the `components` section of the vue component.

```javascript
components: {
  VueSplitter
}
```
Use it in the html, populating the `left-pane` and `right-pane` slots.

```html
<vue-splitter :margin="20">
  <div slot="left-pane">
    <div>Some left content here.</div>
  </div>
  <div slot="right-pane">
    <div>Some right content here.</div>
  </div>
</vue-splitter>
```

The `margin` prop can be used to set the percentage that the dragger should not exceed on either side. Defaults to 10%.

The `horizontal` prop will create a horizontal splitter bar. Defaults to false. Note: An ancestor element (parent, parents parent etc) must have a fixed height for the horizontal splitter to work properly.

The `defaultPercent` prop will set the default starting and reset percentange. Defaults to 50%.

The `resize` event will be fired when the dragger is moved.

## Usage

The dragger can be dragged left and right to move the panes. It can be clicked to reset to the default percent.

## Styles

The following classes may be overridden to apply the style you would like.

The component is wrapped in a `.vue-splitter` class.

Each pane has a `.splitter-pane` class, with a `.left-pane` and `.right-pane` class respectively (corresponds to top and bottom panes for horizontal splitting).

The splitter bar has a `.splitter` class.
