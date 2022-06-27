<template>
  <div class="absolute top-full left-0 z-20 bg-white shadow-md rounded w-fit">
    <div class="px-4 py-2 uppercase text-xs font-bold opacity-50">
      Command Menu
    </div>
    <ul>
      <li
        v-for="menu in menusFiltered"
        :key="menu.id"
        class="px-4 py-2 cursor-pointer hover:bg-gray-100"
        :class="{ 'bg-gray-200': menu.tag === block.tag }"
        @click="selectMenu(menu.tag)"
      >
        <div class="font-bold">{{ menu.label }}</div>
        <div class="opacity-50">{{ menu.description }}</div>
      </li>
    </ul>
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
  computed: {
    menusFiltered() {
      if (this.block.html !== '') {
        return this.menus.filter(item => item.tag !== 'ul')
      } else {
        return this.menus
      }
    }
  },
  data: () => ({
    menus: [
      {
        id: 'text',
        tag: 'p',
        label: 'Text',
        description: 'Just start writing with plain text.'
      },
      {
        id: 'heading-1',
        tag: 'h1',
        label: 'Header 1',
        description: 'Big section heading.'
      },
      {
        id: 'heading-2',
        tag: 'h2',
        label: 'Header 2',
        description: 'Medium section heading.'
      },
      {
        id: 'heading-3',
        tag: 'h3',
        label: 'Header 3',
        description: 'Small section heading.'
      },
      {
        id: 'image',
        tag: 'img',
        label: 'Image',
        description: 'Upload image from link.'
      },
      {
        id: 'list',
        tag: 'ul',
        label: 'List',
        description: 'Create a simple list.'
      }
    ]
  }),
  methods: {
    selectMenu(tag) {
      this.$emit('select-menu', tag)
    }
  }
}
</script>
