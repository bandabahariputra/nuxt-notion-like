<template>
  <div>
    <component
      contenteditable
      :is="block.tag"
      v-on="listeners"
      @keydown.prevent="tes"
    >
      {{ block.html }}
      
    </component>

    <NEditableBlock
        v-for="(item, index) in block.contents"
        :key="index"
        :block="item"
        :depth="index + 1"
        :style="indent"
        :funn="funn"
      />
  </div>
</template>

<script>
import getNewId from "../helpers/getNewId";
export default {
  emits: ['add-block'],
  props: {
    block: Object,
    depth: Number,
    funn: Function
  },
  computed: {
    indent() {
      return {
        transform: `translate(${this.depth * 50}px)`
      }
    },
    listeners() {
      return {
        ...this.$listeners,
        input: this.onInput
      }
    }
  },
  methods: {
    onInput(e) {},
    tes(){
      this.block.contents.push({
        id: getNewId(),
        contents: [],
        tag: "p",
        src: "",
        width: "",
        height: "",
        alt: "",
        listStyle: "",
        html: "fdsffsd",
      })
    }
  }
}
</script>

