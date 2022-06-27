<template>
  <div class="container mx-auto px-6">
    <h1
      class="text-4xl font-bold p-4 text-center border-b border-gray-100"
      style="font-family: 'Oleo Script', cursive;"
      >
      notion like ceunah
    </h1>

    <div class="mt-6">
      <NuxtNotionLike
        :blocks="blocks"
        @add-block="addBlock"
        @delete-block="deleteBlock"
        @update-block="updateBlock"
      />
    </div>
  </div>
</template>

<script>

export default {
  data: () => ({
    blocks: []
  }),
  mounted() {
    const blocks = [
      {
        id: 'header',
        html: 'Ripped Pants',
        tag: 'h1',
        order: 0
      },
      {
        id: 'image',
        html: '',
        tag: 'img',
        src: 'https://source.unsplash.com/random',
        width: '200px',
        height: '200px',
        alt: 'Default Image',
        order: 1,
      },
      {
        id: 'p-1',
        html: 'When big Larry came around just to put him down',
        tag: 'p',
        order: 2
      },
      {
        id: 'p-2',
        html: 'Spongebob turned into a clown',
        tag: 'p',
        order: 3
      },
      {
        id: 'p-3',
        html: 'And no girl ever wants to dance',
        tag: 'p',
        order: 4
      },
      {
        id: 'p-4',
        html: 'With a fool who went and ripped his pants',
        tag: 'p',
        order: 5
      },
      {
        id: 'p-5',
        html: '',
        tag: 'p',
        order: 6
      },
      {
        id: 'ul',
        contents: [
          {
            id: 'li-1',
            html: "I know I shouldn't mope around, I shouldn't curse",
            tag: 'li',
            order: 1
          },
          {
            id: 'li-2',
            html: 'But the pain feels so much worse',
            tag: 'li',
            order: 2
          },
          {
            id: 'li-3',
            html: "'Cause windin' up with no one is a lot less fun",
            tag: 'li',
            order: 3
          },
          {
            id: 'l-4',
            html: 'Than a burn from the sun or sand in your buns!',
            tag: 'li',
            order: 4
          }
        ],
        tag: 'ul',
        listStyle: 'disc',
        order: 7
      },
      {
        id: 'p-6',
        html: '',
        tag: 'p',
        order: 8
      }
    ]

    this.blocks = blocks
  },
  methods: {
    addBlock({ currentBlock, newBlock }) {
      this.blocks = this.blocks.map((item) => {
        return {
          ...item,
          order: item.order > currentBlock.order ? item.order + 1 : item.order
        }
      })

      this.blocks.push(newBlock)

      this.blocks = this.blocks.sort((a, b) => a.order - b.order)
    },
    deleteBlock(currentBlock) {
      const blockIndex = this.blocks.findIndex(item => item.id === currentBlock.id)

      this.blocks.splice(blockIndex, 1)

      this.blocks = this.blocks.map((item) => {
        return {
          ...item,
          order: item.order > currentBlock.order ? item.order - 1 : item.order
        }
      })
    },
    updateBlock(newBlock) {
      this.blocks = this.blocks.map(item => item.id === newBlock.id ? newBlock : item)
    }
  }
}
</script>
