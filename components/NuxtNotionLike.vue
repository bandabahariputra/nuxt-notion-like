<template>
  <div class="nuxt-notion-like">
    <div
      v-for="block in blocks"
      :key="block.id"
      class="relative"
    >
      <ol
        v-if="block.tag === 'ul'"
        :style="{ listStyleType: block.listStyle}"
      >
        <div v-for="item in block.contents" :key="item.id">
          <EditableBlock
            :block="item"
            @add-block="addBlock($event, block, item)"
            @delete-block="deleteBlock({ currentBlock: block, currentItem: item })"
            @update-block="updateBlock({ currentBlock: block, currentItem: item }, ...arguments)"
            @update-link="updateLink({ currentBlock: block, currentItem: item }, ...arguments)"
            @update-list="updateList({ currentBlock: block, currentItem: item }, ...arguments)"
          />
        </div>
      </ol>
      <EditableBlock
        v-else
        :block="block"
        @add-block="addBlock($event, block)"
        @delete-block="deleteBlock({ currentBlock: block })"
        @update-block="updateBlock({ currentBlock: block }, ...arguments)"
        @update-image="updateImage(block, ...arguments)"
        @update-link="updateLink({ currentBlock: block }, ...arguments)"
      />
    </div>
  </div>
</template>

<script>
import EditableBlock from '../components/EditableBlock.vue'
import getNewId from '../helpers/getNewId'

export default {
  components: { EditableBlock },
  props: {
    blocks: {
      type: Array,
      required: true
    },
  },
  methods: {
    addBlock(currentEvent, currentBlock, currentItem = null) {
      if (currentItem) {
        const findItem = currentBlock.contents.findIndex(item => item.id === currentItem.id)
        const isLastItem = currentBlock.contents.length - 1 === findItem
        
        if (currentItem.tag === 'li' && currentItem.html === '' && isLastItem) {
          const newBlock = {
            id: getNewId(),
            html: '',
            tag: 'p',
            order: currentBlock.order + 1
          }
          
          this.$emit('add-block', {
            currentBlock,
            newBlock
          })

          setTimeout(() => {
            currentEvent.blur()
            currentEvent.parentElement.parentElement.parentElement.parentElement.nextSibling.firstChild.firstChild.focus()

            const itemIndex = currentBlock.contents.findIndex(item => item.id === currentItem.id)

            currentBlock.contents.splice(itemIndex, 1)

            this.$emit('update-block', currentBlock)
          }, 0)
        } else if (currentItem.tag === 'li' && currentItem.html !== '') {
          const newList = {
            id: getNewId(),
            html: '',
            tag: 'li',
            order: currentItem.order + 1
          }
          
          currentBlock.contents = currentBlock.contents.map((item) => {
            return {
              ...item,
              order: item.order > currentItem.order ? item.order + 1 : item.order
            }
          })

          currentBlock.contents.push(newList)

          currentBlock.contents = currentBlock.contents.sort((a, b) => a.order - b.order)

          this.$emit('update-block', currentBlock)

          setTimeout(() => {
            currentEvent.blur()
            currentEvent.parentElement.parentElement.nextSibling.firstChild.firstChild.focus()
          }, 0)
        }
      } else {
        const newBlock = {
          id: getNewId(),
          html: '',
          tag: 'p',
          order: currentBlock.order + 1
        }
        
        this.$emit('add-block', {
          currentBlock,
          newBlock
        })

        setTimeout(() => {
          currentEvent.blur()
          currentEvent.parentElement.parentElement.nextSibling.lastChild.firstChild.focus()
        }, 0)
      }
    },
    deleteBlock({ currentBlock, currentItem = null }) {
      if (currentItem && currentBlock.contents.length > 1) {
        const itemIndex = currentBlock.contents.findIndex(item => item.id === currentItem.id)

        currentBlock.contents.splice(itemIndex, 1)

        this.$emit('update-block', currentBlock)
      } else {
        this.$emit('delete-block', currentBlock)
      }
    },
    updateBlock({ currentBlock, currentItem = null }, newBlock) {
      if (currentItem) {
        currentBlock.contents = currentBlock.contents.map(item => item.id === currentItem.id ? newBlock : item)
        this.$emit('update-block', currentBlock)
      } else {
        this.$emit('update-block', {
          ...currentBlock,
          tag: newBlock.tag ? newBlock.tag : currentBlock.html,
          html: newBlock.html ? newBlock.html : currentBlock.html,
          src: newBlock.tag === 'img' ? 'https://source.unsplash.com/random' : '',
          width: newBlock.tag === 'img' ? '200px' : '',
          height: newBlock.tag === 'img' ? '200px' : '',
          alt: newBlock.tag === 'img' ? 'Gambar' : '',
          contents: newBlock.tag === 'ul' ? newBlock.contents : [],
          listStyle: newBlock.tag === 'ul' ? newBlock.listStyle : 'disc',
        })
      }
    },
    updateImage(currentBlock, setting) {
      this.$emit('update-block', {
        ...currentBlock,
        src: setting.src,
        width: setting.width,
        height: setting.height,
        alt: setting.alt
      })
    },
    updateLink({ currentBlock, currentItem = null }, newHtml) {
      if (currentItem) {
        currentBlock.contents = currentBlock.contents.map((item) => {
          return item.id === currentItem.id ? { ...item, html: newHtml } : item
        })

        this.$emit('update-block', currentBlock)
      } else {
        this.$emit('update-block', {
          ...currentBlock,
          html: newHtml
        })
      }
    },
    updateList({ currentBlock, currentItem = null }, type) {
      this.$emit('update-block', {
        ...currentBlock,
        listStyle: type
      })
    }
  }
}
</script>
