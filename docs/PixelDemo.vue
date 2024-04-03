<style lang="scss" scoped>
  .container {
    height: 300px;
    white-space: pre-wrap;
  }
</style>
<template>
  <vue-splitter class="container" v-model:pixel="modelPixel" :initialPixel="initialPixel" :size-pane="sizePane"
    use-pixel>
    <template #left-pane>
      <slot name="left-pane">
        {{lorumipsum}}
      </slot>
    </template>
    <template #right-pane>
      <slot name="right-pane">
        {{lorumipsum}}
      </slot>
    </template>
  </vue-splitter>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue'
import VueSplitter from '../src/vue-splitter.vue'
import { GenLorum } from './Helpers'

const props = withDefaults(defineProps<{
  isHorizontal?: boolean,
  sizePane?: "left" | "right" | "top" | "bottom";
  pixel?: number,
  initialPixel?: number | string,
}>(), {
  sizePane: "right",
  isHorizontal: false,
  initialPixel: 250,
})

const emit = defineEmits<{
  (event: 'update:percent', value: number): void
  (event: 'update:pixel', value: number): void
  (event: 'splitter-click'): void
}>()

const pixel = ref(120);

const modelPixel = computed<number>({
  get() {
    return !isNaN(Number(props.pixel)) ? Number(props.pixel) : pixel.value
  },
  set(value) {
    emit('update:percent', value)
    pixel.value = value
  }
})

const lorumipsum = GenLorum()
</script>