<style lang="scss">
  .vue-splitter {
    display: grid;
    height: inherit;
    &.vertical {
      width: 100%;
      > .splitter {
        cursor: ew-resize;
        width: calc(v-bind(splitterSize) * 1px);
      }
    }
    &.horizontal {
      height: 100%;
      > .splitter {
        cursor: ns-resize;
        height: calc(v-bind(splitterSize) * 1px);
      }
    }
    .splitter {
      background-color: #9e9e9e;
    }
    .splitter-pane {
      overflow-y: auto;
    }
  }
</style>
<template>
  <div
    :style="{ userSelect, gridTemplate }"
    class="vue-splitter"
    :class="{ horizontal: isHorizontal, vertical: !isHorizontal }"
    ref="containerRef"
  >
    <div
      class="splitter-pane"
      :class="leftPaneClass"
    >
      <slot name="left-pane"></slot>
      <slot name="top-pane"></slot>
    </div>
    <div
      class="splitter"
      :class="{ active: isActive }"
      @mousedown="onSplitterMouseDown"
      @touchstart.passive="onSplitterTouchDown"
      @click="onSplitterClick"
    />
    <div
      class="splitter-pane"
      :class="rightPaneClass">
      <slot name="right-pane"></slot>
      <slot name="bottom-pane"></slot>
    </div>
  </div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue'

const props = withDefaults(defineProps<{
  isHorizontal?: boolean,
  sizePane?: "left" | "right" | "top" | "bottom";
  usePixel?: boolean,
  percent?: number,
  pixel?: number,
  initialPercent?: number | string,
  initialPixel?: number | string,
  splitterSize?: number;
}>(), {
  sizePane: "left",
  usePixel: false,
  isHorizontal: false,
  initialPercent: 50,
  initialPixel: 250,
  splitterSize: 5,
})

const emit = defineEmits<{
  (event: 'update:percent', value: number): void
  (event: 'update:pixel', value: number): void
  (event: 'splitter-click'): void
}>()

const isActive = ref(false)
const percent = ref(50)
const pixel = ref(250)
const hasMoved = ref(false)
const dragOffset = ref(0)

const containerRef = ref<HTMLElement>()

const modelPercent = computed<number>({
  get() {
    return props.percent ?? percent.value
  },
  set(value) {
    emit('update:percent', value)
    percent.value = value
  }
})

modelPercent.value = Number(props.initialPercent)

const modelPixel = computed<number>({
  get() {
    return props.pixel ?? pixel.value
  },
  set(value) {
    emit('update:pixel', value)
    pixel.value = value
  }
})

modelPixel.value = Number(props.initialPixel)


const templateSizes = computed<string>(() => {
  const size = props.usePixel ? `${modelPixel.value}px` : `${modelPercent.value}%`;
  return ["top", "left"].includes(props.sizePane)  ? `${size} auto 1fr` : `1fr auto ${size}`
});

const leftPaneClass = computed(() => props.isHorizontal ? 'top-pane' : 'left-pane')
const rightPaneClass = computed(() => props.isHorizontal ? 'bottom-pane' : 'right-pane')
const gridTemplate = computed(() => props.isHorizontal ? `${templateSizes.value} / none` : `none / ${templateSizes.value}`)
const userSelect = computed(() => isActive.value ? 'none' : 'auto')

function onSplitterClick() {
  if (!hasMoved.value) {
    emit('splitter-click')
  }
}

function onSplitterMouseDown(e: MouseEvent) {
  dragOffset.value = props.isHorizontal ? e.offsetY : e.offsetX
  onSplitterDown()
}

function onSplitterTouchDown() {
  dragOffset.value = 0
  onSplitterDown()
}

function onSplitterDown() {
  isActive.value = true
  hasMoved.value = false
  addBodyListeners()
}

function addBodyListeners() {
  document.body.addEventListener('mousemove', onBodyMouseMove)
  document.body.addEventListener('touchmove', onBodyTouchMove)
  document.body.addEventListener('touchend', onBodyUp, { once: true })
  document.body.addEventListener('mouseup', onBodyUp, { once: true })
}

function onBodyTouchMove(e: TouchEvent) {
  if (isActive.value) {
    calculateSplitterPercent(e.touches[0])
  }
}

function onBodyMouseMove(e: MouseEvent) {
  if (e.buttons && e.buttons === 0) {
    isActive.value = false
    removeBodyListeners()
  }
  if (isActive.value) {
    calculateSplitterPercent(e)
  }
}

function calculateSplitterPercent(e: MouseEvent | Touch) {
  let offset = dragOffset.value
  let target = containerRef.value as HTMLElement
  let containerOffset = 0;
  let pixel = 0
  if (props.isHorizontal) {
    offset += target.offsetTop
    while (target.offsetParent) {
      target = target.offsetParent as HTMLElement
      offset += target.offsetTop
    }
    pixel = (e.pageY - offset);
    containerOffset = containerRef.value!.offsetHeight;
  } else {
    offset += target.offsetLeft
    while (target.offsetParent) {
      target = target.offsetParent as HTMLElement
      offset += target.offsetLeft
    }
    pixel = (e.pageX - offset);
    containerOffset = containerRef.value!.offsetWidth;
  }
  const percent = Math.floor((pixel / containerOffset) * 10000) / 100
  const splitterSizeInPercent = Math.floor((props.splitterSize / containerOffset) * 10000) / 100

  if (percent > 0 && percent < 100) {
    const sizeLastPane = ["bottom", "right"].includes(props.sizePane)
    modelPercent.value = sizeLastPane ? 100 - percent - splitterSizeInPercent : percent
    modelPixel.value = sizeLastPane ? containerOffset - pixel - props.splitterSize : pixel
    hasMoved.value = true
  }
}

function onBodyUp() {
  isActive.value = false
  removeBodyListeners()
}

function removeBodyListeners() {
  document.body.removeEventListener('touchmove', onBodyTouchMove)
  document.body.removeEventListener('mousemove', onBodyMouseMove)
}

</script>
