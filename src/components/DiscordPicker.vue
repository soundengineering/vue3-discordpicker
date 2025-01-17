<template>
  <div class="vue3-emojipicker" :style="input ? 'width: 100%;' : 'max-width: max-content'">
    <div :class="`relative ${input ? 'w-full' : 'max-w-max'} font-sans`">
      <div
        :class="{ 'opacity-0 pointer-events-none': !opened }"
        class="vue3-discord-emojipicker rounded-xl shadow-lg overflow-hidden mx-auto absolute transition duration-200 -top-4 right-0 transform -translate-y-full"
        v-click-outside="close"
      >
        <header class="bg-grey-400 px-5 pt-5 pb-2 shadow-lg relative z-1 flex items-center">
          <p
            v-if="apiKey"
            :class="{ 'hover:bg-opacity-100 hover:bg-grey-300 bg-grey-300': active === 'gif' }"
            class="font-bold text-white text-sm py-1 hover:bg-grey-300 hover:bg-opacity-40 px-2 rounded-md cursor-pointer w-max hidden md:block mr-3"
            @click="active = 'gif'"
          >
            GIF
          </p>
          <p
            v-if="showEmoji"
            :class="{ 'hover:bg-opacity-100 hover:bg-grey-300 bg-grey-300': active === 'emoji' }"
            class="font-bold text-white text-sm py-1 hover:bg-grey-300 hover:bg-opacity-40 px-2 rounded-md cursor-pointer w-max hidden md:block"
            @click="active = 'emoji'"
          >
            Emoji
          </p>
        </header>
        <gif-picker v-if="active === 'gif'" :api-key="apiKey" :sources="$data.$sources" @send="({ url, send, type}) => this.send(url, send, type)" />
        <emoji-picker v-if="active === 'emoji'" :categories="categories" :emojis="emojis" :sources="$data.$sources" @send="({ emoji, send }) => this.send(emoji, send)" />
      </div>
      <div :class="{ 'bg-grey-400 rounded-xl justify-between pr-4 flex items-center emojibutton__active': input }" class="mt-4">
        <upload-button v-if="showUpload" @click="$emit('upload')" />
        <Autocomplete v-if="input" :value="value" :placeholder="placeholder" :opened-picker="opened" :emojis="emojis.list" @change="e => $emit('update:value', e)" @send="send" @close="close" />
        <div class="flex items-center justify-center">
          <slot />
          <gif-button v-if="apiKey" :sources="$data.$sources" @click="open" />
          <emoji-button v-if="showEmoji" :sources="$data.$sources" class="ml-3" @click="open" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue'

import clickOutside from '@/directives/click-outside.js'
import emojis from '@/assets/emojis.json'
import EmojiPicker from '@/components/emojis/EmojiPicker.vue'
import GifPicker from '@/components/gif/GifPicker.vue'
import EmojiButton from '@/components/emojis/Button.vue'
import GifButton from '@/components/gif/Button.vue'
import UploadButton from '@/components/upload/Button.vue'
import Autocomplete from '@/components/emojis/Autocomplete.vue'

export default defineComponent({
  components: { EmojiButton, Autocomplete, GifButton, GifPicker, EmojiPicker, UploadButton },
  directives: {
    clickOutside,
  },
  emits: ['update:value', 'emoji', 'gif', 'upload'],
  props: {
    categories: {
      type: Array,
      default () {
        return ['people','animals','foods','travel','activities','objects','symbols','flags']
      }
    },
    placeholder: {
      type: String,
      default: 'Type your message'
    },
    input: {
      type: Boolean,
      default: false
    },
    value: {
      type: [String, Number],
      default: null,
    },
    gifFormat: {
      type: String
    },
    apiKey: {
      type: String
    },
    showUpload: {
      type: Boolean,
      default: false
    },
    showEmoji: {
      type: Boolean,
      default: true
    },
    sources: {
      type: Object
    },
    multiple: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      opened: false,
      emojis,
      active: 'gif',
      $sources: {
        search: 'https://rvrb.one/vue-discord-emojipicker/search.svg',
        gif: 'https://rvrb.one/vue-discord-emojipicker/gif.svg',
        category: 'https://rvrb.one/vue-discord-emojipicker/categories/%REPLACE%.svg',
        variation: 'https://rvrb.one/vue-discord-emojipicker/variations/variation_%REPLACE%.svg',
        emoji: 'https://rvrb.one/vue-discord-emojipicker/sprite_emojis.png'
      }
    }
  },
  created () {
    this.$data.$sources = {
      ...this.$data.$sources,
      ...this.sources
    }
  },
  methods: {
    close () {
      if (this.opened) {
        this.opened = false
      }
    },
    send (value, send = false, type = 'emoji') {
      if (type === 'gif') {
        value = this.formatGif(value)
        this.$emit('gif', value)
      }
      if (type === 'emoji') {
        value = (value.variations && this.variation > 0) ? value.variations[this.variation] : value.emoji
        this.$emit('emoji', value)
        if (this.input && send) {
          this.$emit('update:value', `${this.value} ${value}`)
        }
      }
      if (!this.multiple || type === 'gif') {
        this.opened = false
      }
    },
    formatGif (url) {
      if (this.gifFormat === 'md') {
        url = `!(alt)[${url}]`
      }
      if (this.gifFormat === 'html') {
        url = `<img src="${url}" />`
      }
      return url
    },
    open (item = 'emoji') {
      if (this.active !== item) {
        this.active = item
        this.opened = true
        return
      }
      this.opened = !this.opened
    }
  }
})
</script>


<style scoped>
@tailwind base;
@tailwind components;
@tailwind utilities;

.vue3-discord-emojipicker {
  height: 454px;
  width: 444px;
}
@media (max-width: 768px) {
  .vue3-discord-emojipicker {
    width: 300px;
    height: 400px;
  }
}
#vue3-discordpicker {
  scroll-behavior: smooth;
}

.vue3-emojipicker .mt-4 {
  margin-top: 0 !important;
}

.vue3-emojipicker .absolute {
  top: -10px !important;
}

.vue3-emojipicker {
  padding-right: 10px !important;
}

.z-1 {
  z-index: 1;
}
</style>