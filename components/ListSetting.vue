<template>
  <div
    class="w-full h-screen fixed top-0 left-0 bg-black bg-opacity-25 z-10 grid place-items-center"
    @click.self="handleClose"
  >
    <div class="bg-white shadow-md rounded w-72">
      <div class="px-4 py-2 uppercase text-xs font-bold opacity-50">
        List Settings
      </div>
      <div class="px-4 py-2 flex flex-col gap-4">
        <div class="w-full flex item-center">
          <div class="w-2/5 text-sm mt-0.5">List style type</div>
          <div class="w-3/5 flex gap-2">
            <span>:</span>
            <select
              v-model="selectedListType"
              class="w-full outline-none border-b border-gray-200"
            >
              <option v-for="item in listTypes" :key="item">{{ item }}</option>
            </select>
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
    }
  },
  data() {
    return {
      selectedListType: 'disc',
      listTypes: [
        'none',
        'disc',
        'circle',
        'square',
        'decimal'
      ]
    }
  },
  methods: {
    handleApply() {
      this.$emit('apply-setting', {
        type: this.selectedListType
      })
    },
    handleClose() {
      this.$emit('close-setting')
    }
  }
}
</script>
