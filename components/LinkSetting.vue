<template>
  <div
    class="w-full h-screen fixed top-0 left-0 bg-black bg-opacity-25 z-10 grid place-items-center"
    @click.self="handleClose"
  >
    <div class="bg-white shadow-md rounded w-fit">
      <div class="px-4 py-2 uppercase text-xs font-bold opacity-50">
        Link Settings
      </div>
      <div class="px-4 py-2 flex flex-col gap-4">
        <div class="w-full flex item-center">
          <div class="w-1/3 text-sm mt-1">Text</div>
          <div class="w-2/3 flex gap-2">
            <span>:</span>
            <span>{{ text }}</span>
          </div>
        </div>
        <div class="w-full flex item-center">
          <div class="w-1/3 text-sm mt-1">Link</div>
          <div class="w-2/3 flex gap-2">
            <span>:</span>
            <input
              type="text"
              v-model="newLink"
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
  </div>
</template>

<script>
export default {
  props: {
    block: {
      type: Object,
      required: true
    },
    text: {
      type: String,
      required: true
    },
    link: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      newLink: this.link
    }
  },
  methods: {
    handleApply() {
      this.$emit('apply-setting', {
        link: this.newLink
      })
    },
    handleClose() {
      this.$emit('close-setting')
    }
  }
}
</script>
