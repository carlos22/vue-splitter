<style lang="scss">
  .styling.vue-splitter {
    * {
      padding: 1rem;
      border: 1px solid #ccc;
    }
    .splitter {
      width: 30px;
      background: blue;
      &:hover {
        background: red;
      }
      &.active {
        background: green;
      }
    }
  }
</style>
<script setup>
import { reactive, computed } from 'vue'
import BaseDemo from './BaseDemo.vue'
import NestingDemo from './NestingDemo.vue'
import PixelDemo from './PixelDemo.vue'
import VueSplitter from '../src/vue-splitter.vue'

const propsPercentData = reactive({
  percent: 25
})

const nestedData = reactive({
  isHorizontal: true
})

const isHorizontalData = reactive({
  isHorizontal: true
})

const eventData = reactive({
  percent: 50,
  percentLogs: ["Drag the splitter to see the events!"],
  clickPercent: 50,
  clickLogs: ["Click the splitter to see the events!"]
})

const helpersMinWidthData = reactive({
  percent: 50,
})

const helpersResetOnClickData = reactive({
  percent: 25,
})

function onHelpersResetOnClick() {
  helpersResetOnClickData.percent = 50
}

const helpersMinWidthCompPercent = computed({
  get() {
    return helpersMinWidthData.percent
  },
  set(val) {
    helpersMinWidthData.percent = Math.max(20, Math.min(60, val))
  }
})
function leftPad(num) {
  return num < 10 ? `0${num}` : num
}

function addToLog(event, logs) {
  const d = new Date()
  logs.unshift(`${d.getHours()}:${leftPad(d.getMinutes())}:${leftPad(d.getMinutes())}:${d.getMilliseconds()} - ${event}`)
}

function formatLogs(logs) {
  return logs.join('\n')
}

const stylingData = reactive({
  percent: 50
})

const stylingCompPercent = computed({
  get() {
    return stylingData.percent
  },
  set(val) {
    stylingData.percent = Math.max(20, Math.min(80, val))
  }
})

</script>

## Installation

::: info
VueSplitter is compatible with Vue 3 only.
:::

Install using npm or yarn.

```bash
npm i @rmp135/vue-splitter
```

Import the component and styling into your component.

```js
import VueSplitter from '@rmp135/vue-splitter'
```

## Usage

### Basic

The simplest setup is shown below. By default in vertical orientation two slots are available to use, `left-pane` and `right-pane`.

Populate these with content for the respective slot.

Dragging the splitter bar will shift the content accordingly. You can continue to drag outside the container.

<BaseDemo />

```vue
<template>
  <vue-splitter>
    <template #left-pane>
      Left Pane content
    </template>
    <template #right-pane>
      Right Pane content
    </template>
  </vue-splitter>
</template>
```

### Pixel

The `use-pixel` control wether to use pixels or percent. The `pixel` v-model behaves similar to the `percent` one.
To control which of the panes will be absolute sized use the `size-pane` prop.

```vue
<template>
  <vue-splitter use-pixel size-pane="right">
    <template #left-pane>
      Left Pane content
    </template>
    <template #right-pane>
      Right Pane content
    </template>
  </vue-splitter>
</template>
```

<PixelDemo />

### Nesting

Splitter views can be nested within other splitter views. To make a splitter horizontal see the prop [is-horizontal](#is-horizontal).

<NestingDemo />

```vue
<template>
  <vue-splitter>
    <template #left-pane>
      Left Pane content
    </template>
    <template #right-pane>
      <vue-splitter is-horizontal>
        <template #top-pane>
          Top Pane content
        </template>
        <template #bottom-pane>
          Bottom Pane content
        </template>
      </vue-splitter>
    </template>
  </vue-splitter>
</template>
```

## Props

### percent

- type: `Number`

Binds how far, in percent of the component container, the splitter bar will be.

::: warning
When used, you must two-way bind the value using `v-model:percent` to allow the value to update.
:::

Current Percent: {{propsPercentData.percent}}
<button v-on:click="propsPercentData.percent = 50">Set to 50%</button>
<base-demo v-model:percent="propsPercentData.percent"/>

```vue
<template>
  <vue-splitter v-model:percent="percent">
  ...
  </vue-splitter>
</template>
<script setup>
import { ref } from 'vue'

const percent = ref(50)
</script>
```

### initial-percent

- type: `Number`

Sets the initial percentage of the splitter bar. This allows for a first load static value without having to two-way bind the value.

<base-demo initial-percent="20"/>

```vue
<vue-splitter initial-percent="20">
...
</vue-splitter>
```

### use-pixel

- type: `Boolean`

If true the `pixel` prop will be used instead of the `percent`.

### pixel

- type: `Number`

Binds how far, in pixels of the component container, the splitter bar will be. Also available in the percent mode, can
be used to limit the minimum or maximum in pixels.

::: warning
When used, you must two-way bind the value using `v-model:pixel` to allow the value to update.
:::

Otherwise very similar to `percent`

### initial-pixel

- type: `Number`

Sets the initial pixel of the splitter bar. This allows for a first load static value without having to two-way bind the value.

### size-pane

- type: `String`

Defines which of the panes will be sizes, that is important for the pixel mode as the other pane will enlarge / shrink.
Valid values are `top` `left` `right` `bottom`.


### is-horizontal

- type: `Boolean`
- default: `false`

Displays the splitter as a horizontal bar, placing content on top and below.

::: tip
For ease of use, the slot names can be referred to as `top-pane` and `bottom-pane` respectively.
:::

<button v-on:click="isHorizontalData.isHorizontal = !isHorizontalData.isHorizontal">Toggle</button>
<base-demo :is-horizontal="isHorizontalData.isHorizontal"/>

<template v-if="isHorizontalData.isHorizontal">

```vue
<template>
  <vue-splitter :is-horizontal="true">
    <template #top-pane>
      Top Pane content
    </template>
    <template #bottom-pane>
      Bottom Pane content
    </template>
  </vue-splitter>
</template>
```
</template>
<template v-else>

```vue
<template>
  <vue-splitter :is-horizontal="false">
    <template #left-pane>
      Left Pane content
    </template>
    <template #right-pane>
      Right Pane content
    </template>
  </vue-splitter>
</template>
```
</template>

::: danger
Horizontal mode does not work well on touch screen devices. The page will insist on scrolling.
:::

## Events

### update:percent

- type: Number

Triggers when the splitter bar moves. See prop [percent](#percent).
<base-demo v-model:percent="eventData.percent" v-on:update:percent="addToLog($event + '%', eventData.percentLogs)">
  <template v-slot:left-pane>{{formatLogs(eventData.percentLogs)}}</template>
</base-demo>

```vue
<vue-splitter @update:percent="onUpdatePercent">
...
</vue-splitter>
```

### update:pixel

- type: Number

Triggers when the splitter bar moves. See prop [pixel](#pixel).

### splitter-click

- type: void

Triggers when the splitter bar is clicked.
<base-demo v-model:percent="eventData.clickPercent" v-on:splitter-click="addToLog('clicked', eventData.clickLogs)">
  <template v-slot:left-pane>{{formatLogs(eventData.clickLogs)}}</template>
</base-demo>

```vue
<vue-splitter @splitter-click="onSplitterClick">
...
</vue-splitter>
```

## Styling

Classes are applied to the splitter container, panes and bar.

`.vue-splitter` - The entire component.

`.vue-splitter.vertical` - When the splitter is in vertical mode.

`.vue-splitter.horizontal` - When the splitter is in horizontal mode.

`.splitter-pane` - Applied to both panes.

`.splitter-pane.left-pane` - Container for the left pane in vertical mode.

`.splitter-pane.top-pane` - Container for the top pane in horizontal mode.

`.splitter-pane.right-pane` - Container for the right pane in vertical mode.

`.splitter-pane.bottom-pane` - Container for the bottom pane in horizontal mode.

`.splitter` - The splitter bar itself.

`.splitter.active` - When the bar is being actively moved.

<base-demo v-model:percent="stylingCompPercent" class="styling" />
```scss
.styling.vue-splitter {
  .splitter {
    width: 30px;
    background: blue;
    &:hover {
      background: red;
    }
    &.active {
      background: green;
    }
  }
}
```

::: warning
Adding padding with a larger splitter bar may cause layout issues. The above limits the panes to set [min widths](#min-width).
:::

## Removed Props

Some props were removed from version 1 of VueSplitter to make the component more flexible. Here's how you can achieve the same result with more flexibility.

### Min Width

To set the minimum width of each pane, bind a computed property to the [percent](#percent) prop.

The below has been limited between 20% and 60%.

Percent: {{helpersMinWidthData.percent}}%

<base-demo v-model:percent="helpersMinWidthCompPercent" />

```vue
<template>
    <vue-splitter v-model:percent="limitedPercent" />
</template>
<script setup>
import { ref, computed } from 'vue'

const percent = ref(50)
const limitedPercent = computed({
  get() {
    return percent.value
  },
  set(val) {
    percent.value = Math.max(20, Math.min(60, val))
  }
})
</script>

```

### Reset on Click

To reset the bar on click, listen the [splitter-click](#splitter-click) event and react accordingly.

The below will reset to 50% when the splitter bar is clicked.

Percent: {{helpersResetOnClickData.percent}}%

<base-demo
  v-on:splitter-click="onHelpersResetOnClick"
  v-model:percent="helpersResetOnClickData.percent"
  :initial-percent="helpersResetOnClickData.percent"
/>

```vue
<template>
    <vue-splitter
      v-model:percent="percent"
      @splitter-click="onSplitterClick"
    />
</template>
<script setup>
import { ref } from 'vue'

const percent = ref(25)

function onSplitterClick() {
  percent.value = 50
}
</script>

```