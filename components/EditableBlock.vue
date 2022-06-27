<template>
  <div class="relative">
    <component
      ref="editable"
      :class="{
        'cursor-pointer': block.tag === 'img',
        'block-component': block.tag !== 'img'
      }"
      contenteditable
      :id="block.id"
      :is="block.tag"
      :placeholder="_placeholder"
      :src="block.tag === 'img' ? block.src ? block.src : 'https://source.unsplash.com/random' : ''"
      :width="block.tag === 'img' ? block.width : ''"
      :height="block.tag === 'img' ? block.height : ''"
      :alt="block.tag === 'img' ? block.alt : ''"
      :style="{ 'margin': block.tag === 'img' ? '0 auto' : '' }"
      v-on="listeners"
      v-html="html"
      @keydown="onKeyDown"
      @focus="focused = true"
      @blur="onBlur"
      @mouseup="onMouseUp"
    />

    <!-- actions -->
    <div
      v-if="focused"
      class="absolute bottom-full left-0 z-10 flex item-center gap-2"
    >
      <!-- turn to and remove -->
      <div class="bg-gray-100 px-4 py-2 shadow rounded flex gap-4">
        <div
          v-if="block.tag !== 'li'"
          class="cursor-pointer hover:text-gray-900 uppercase text-xs flex items-center"
          :class="[ showCommandMenu ? 'text-gray-900' : 'text-gray-400' ]"
          @click="handleTurnTo"
        >
          turn to
        </div>
        <div
          class="cursor-pointer text-gray-400 hover:text-gray-900 uppercase text-xs flex items-center"
          @click="deleteBlock"
        >
          delete
        </div>
      </div>

      <!-- link -->
      <div
        v-if="block.tag !== 'img' && selectedText"
        class="bg-gray-100 px-4 py-2 shadow rounded flex gap-4"
      >
        <div
          class="cursor-pointer text-gray-400 hover:text-gray-900 uppercase text-xs flex items-center"
          @click="handleCreateLink"
        >
          create link
        </div>
        <div
          v-if="selectedText && selectedLink"
          class="cursor-pointer text-gray-400 hover:text-gray-900 uppercase text-xs flex items-center"
          @click="handleDeleteLink"
        >
          delete link
        </div>
        <div
          v-if="selectedText && selectedLink"
          class="cursor-pointer text-gray-400 hover:text-gray-900 uppercase text-xs flex items-center"
          @click="handleOpenLink"
        >
          open link
        </div>
      </div>

      <!-- list -->
      <div
        v-if="block.tag === 'li'"
        class="bg-gray-100 px-4 py-2 shadow rounded flex gap-4 cursor-pointer text-gray-400 hover:text-gray-900 uppercase text-xs flex items-center"
        @click="handleOpenListSettings"
      >
        list settings
      </div>
    </div>

    <!-- command menu -->
    <CommandMenu
      v-if="showCommandMenu"
      :block="block"
      @select-menu="updateTag"
    />

    <!-- image setting -->
    <div
      v-if="block.tag === 'img'"
      class="absolute top-0 right-0 z-10 bg-gray-100 px-4 py-2 shadow rounded flex gap-4 cursor-pointer text-gray-400 hover:text-gray-900 uppercase text-xs flex items-center"
      @click="showImageSettingToggle"
    >
      image settings
    </div>
    <ImageSetting
      v-if="showImageSetting"
      :block="block"
      @apply-setting="handleApplyImageSetting"
    />

    <!-- link setting -->
    <LinkSetting
      v-if="block.tag !== 'img' && showLinkSetting"
      :block="block"
      :text="selectedText"
      :link="selectedLink"
      @apply-setting="handleApplyLinkSetting"
      @close-setting="handleCloseLinkSetting"
    />

    <!-- list setting -->
    <ListSetting
      v-if="block.tag === 'li' && showListSetting"
      :block="block"
      @apply-setting="handleApplyListSetting"
      @close-setting="handleCloseListSetting"
    />

    <!-- link not applied -->
    <Modal v-if="showLinkNotApplied">
      <div class="text-white text-xs font-bold uppercase">
        link not applied
      </div>
    </Modal>
  </div>
</template>

<script>
import CommandMenu from '../components/CommandMenu.vue'
import ImageSetting from '../components/ImageSetting.vue'
import LinkSetting from '../components/LinkSetting.vue'
import Modal from '../components/Modal.vue'
import getNewId from '../helpers/getNewId'

export default {
  components: {
    CommandMenu,
    ImageSetting,
    LinkSetting,
    Modal
  },
  props: {
    block: {
      type: Object,
      required: true
    }
  },
  data: () => ({
    html: '',
    focused: false,
    showCommandMenu: false,
    showCommandMenuTurnTo: false,
    showImageSetting: false,
    selectedText: null,
    selectedLink: '',
    showLinkSetting: false,
    showLinkSettingCreateLink: false,
    showLinkNotApplied: false,
    showListSetting: false
  }),
  computed: {
    listeners() {
      return {
        ...this.$listeners,
        input: this.onInput
      }
    },
    _placeholder() {
      if (this.block.html === '' && this.block.tag !== 'p') {
        return this.block.tag
      }

      if (this.focused) {
        return "Type '/' for commands"
      }

      return ''
    }
  },
  mounted() {
    this.html = this.block.html
  },
  methods: {
    setCaretToEnd(element) {
      const range = document.createRange()
      const selection = window.getSelection()
      range.selectNodeContents(element)
      range.collapse(false)
      selection.removeAllRanges()
      selection.addRange(range)
      element.focus()
    },
    getSelectedText() {
      if (window.getSelection) {
        return window.getSelection().toString()
      } else if (document.selection) {
        return document.selection.createRange().text
      }

      return ''
    },
    onInput(e) {
      this.$emit('update-block', {
        ...this.block,
        html: e.target.innerHTML
      })
    },
    onKeyDown(e) {
      switch (e.key) {
        case '/':
          if (this.block.html === '') {
            e.preventDefault()
            this.focused = true
            this.showCommandMenu = true
          }
          break
        case 'Enter':
          e.preventDefault()
          this.$emit('add-block', e.target)
          break
        default:
          break
      }
    },
    onBlur() {
      setTimeout(() => {
        if (this.showCommandMenuTurnTo) {
          this.showCommandMenuTurnTo = false
        } else if (this.showLinkSettingCreateLink) {
          this.showLinkSettingCreateLink = false
        } else {
          this.focused = false
          this.showCommandMenu = false
          this.selectedText = null
          this.selectedLink = ''
        }
      }, 200)
    },
    onMouseUp(e) {
      const tagName = e.target.tagName

      if (this.getSelectedText() !== '') {
        this.selectedText = this.getSelectedText()

        if (tagName === 'A' && this.selectedText === e.target.innerText) {
          const link = e.target.getAttribute('href')
          this.selectedLink = link
        }
      } else if (tagName === 'A') {
        this.selectedText = e.target.innerText
        const link = e.target.getAttribute('href')
        this.selectedLink = link
      } else {
        this.selectedText = null
        this.selectedLink = ''
      }
    },
    handleOpenLink(e) {
      window.open(this.selectedLink)

      this.selectedText = null
      this.selectedLink = ''
    },
    handleTurnTo() {
      if (this.showCommandMenu) {
        this.focused = false
        this.showCommandMenu = false
      } else {
        this.showCommandMenu = true
        this.showCommandMenuTurnTo = true
      }

    },
    handleCreateLink() {
      this.showLinkSettingCreateLink = true
      this.showLinkSetting = true
    },
    deleteBlock() {
      this.$emit('delete-block')
    },
    showImageSettingToggle() {
      this.showImageSetting = !this.showImageSetting

      if (this.showImageSetting) {
        this.$refs.editable.focus()
        this.focused = true
        this.showImageSetting = true
      } else {
        this.$refs.editable.blur()
        this.focused = false
        this.showImageSetting = false
      }
    },
    updateTag(tag) {
      if (tag === 'ul') {
        const newList = {
          id: getNewId(),
          html: '',
          tag: 'li',
          order: 1
        }

        const newUl = {
          contents: [ newList ],
          tag: 'ul',
          listStyle: 'disc',
          html: ''
        }

        this.$emit('update-block', {
          ...this.block,
          ...newUl
        })

        this.focused = false
        this.showCommandMenu = false
      } else {
        this.$emit('update-block', {
          ...this.block,
          tag
        })

        this.focused = false
        this.showCommandMenu = false
        this.showImageSetting = false

        setTimeout(() => {
          this.$refs.editable.innerHTML = this.block.html
        }, 0)
      }
    },
    handleApplyImageSetting(setting) {
      this.$emit('update-image', setting)
      this.focused = false
      this.showImageSetting = false
    },
    handleApplyLinkSetting(setting) {
      let newHtml = this.block.html

      if (setting.link !== '') {
        const linkHtml = `<a href="${setting.link}">${this.selectedText}</a>`
        newHtml = newHtml.replace(this.selectedText, linkHtml)
      } else {
        const linkHtml = `<a href="${this.selectedLink}">${this.selectedText}</a>`
        newHtml = newHtml.replace(linkHtml, this.selectedText)
      }

      this.$emit('update-link', newHtml)
      this.focused = false
      this.showLinkSetting = false
      this.selectedText = null
      this.selectedLink = ''

      if (this.block.html === newHtml) {
        this.showLinkNotApplied = true

        setTimeout(() => {
          this.showLinkNotApplied = false
        }, 750)
      }

      setTimeout(() => {
        this.$refs.editable.innerHTML = this.block.html
      }, 0)
    },
    handleDeleteLink() {
      this.handleApplyLinkSetting({ link: '' })
    },
    handleCloseLinkSetting() {
      this.focused = false
      this.showLinkSetting = false
      this.selectedText = null
      this.selectedLink = ''
    },
    handleOpenListSettings() {
      this.showListSetting = true
    },
    handleApplyListSetting(setting) {
      this.$emit('update-list', setting.type)
      this.focused = false
      this.showListSetting = false
    },
    handleCloseListSetting() {
      this.focused = false
      this.showListSetting = false
    }
  }
}
</script>
