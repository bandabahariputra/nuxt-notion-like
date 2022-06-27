<template>
  <div class="absolute top-0 right-36 z-20 bg-white shadow-md rounded w-fit">
    <div class="px-4 py-2 uppercase text-xs font-bold opacity-50">
      Image Settings
    </div>
    <div class="px-4 py-2 flex flex-col gap-4">
      <div class="w-full flex item-center">
        <div class="w-1/3 text-sm mt-1">Image</div>
        <div class="w-2/3 flex gap-2">
          <span>:</span>
          <input
            type="text"
            v-model="src"
            class="w-full outline-none border-b border-gray-200"
          >
        </div>
      </div>
      <div class="w-full flex item-center">
        <div class="w-1/3 text-sm mt-1">Width</div>
        <div class="w-2/3 flex gap-2">
          <span>:</span>
          <input
            type="text"
            v-model="width"
            class="w-full outline-none border-b border-gray-200"
          >
        </div>
      </div>
      <div class="w-full flex item-center">
        <div class="w-1/3 text-sm mt-1">Height</div>
        <div class="w-2/3 flex gap-2">
          <span>:</span>
          <input
            type="text"
            v-model="height"
            class="w-full outline-none border-b border-gray-200"
          >
        </div>
      </div>
      <div class="w-full flex item-center">
        <div class="w-1/3 text-sm mt-1">Alt</div>
        <div class="w-2/3 flex gap-2">
          <span>:</span>
          <input
            type="text"
            v-model="alt"
            class="w-full outline-none border-b border-gray-200"
          >
        </div>
      </div>
    </div>
    <div class="px-4 py-2">
      <button
        type="button"
        class="bg-blue-400 hover:bg-blue-500 text-white text-sm w-full p-2 rounded uppercase"
        @click="handleApply"
      >
        terapkan
      </button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    block: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      src: this.block.src,
      width: this.block.width,
      height: this.block.height,
      alt: this.block.alt
    }
  },
  methods: {
    handleApply() {
      this.$emit('apply-setting', {
        src: this.src,
        width: this.width,
        height: this.height,
        alt: this.alt
      })
    }
  }
}
</script>
