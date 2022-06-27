<template>
  <div class="nuxt-notion-like container mx-auto p-6">
    <NEditableBlock
      v-for="(block, index) in blocks"
      :key="index"
      :block="block"
      :depth="index + 1"
      :funn="onKeyDown"
    />
  </div>
</template>

<script>
import getNewId from "../helpers/getNewId";

export default {
  data: () => ({
    blocks: [
      {
        id: '1',
        contents: [
          {
            id: '11',
            contents: [],
            tag: "p",
            src: "",
            width: "",
            height: "",
            alt: "",
            listStyle: "",
            href: "",
            html: "Child",
          },
        ],
        tag: "p",
        src: "",
        width: "",
        height: "",
        alt: "",
        listStyle: "",
        href: "",
        html: "Notion",
      },
    ],
  }),
  methods: {
    onKeyDown(e) {
      console.log(e.id)
      switch (e.key) {
        case 'Enter':
          e.preventDefault()
          this.$emit('add-block', this.block)
          break;
      
        default:
          break;
      }
    },
    addBlock(currentBlock) {
      console.log(currentBlock.id);
      const update = (array, id, object) =>
        array.some((o) =>
          o.id === id ? o.contents.push(object) : update(o.contents, id, object)
        );

      update(this.blocks, currentBlock.id, {
        id: getNewId(),
        contents: [],
        tag: "p",
        src: "",
        width: "",
        height: "",
        alt: "",
        listStyle: "",
        html: "fdsffsd",
      });
      // const blockIndex = this.blocks.findIndex(item => item.id === currentBlock.id)

      // this.blocks.splice(blockIndex + 1, 0, {
      //   id: getNewId(),
      //   contents: [],
      //   tag: 'p',
      //   src: '',
      //   width: '',
      //   height: '',
      //   alt: '',
      //   listStyle: '',
      //   html: ''
      // })
    },
  },
};
</script>
