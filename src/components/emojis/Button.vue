<script>
import { defineComponent } from 'vue'

export default defineComponent({
  emits: ['click'],
  props: {
    sources: {
      type: Object,
      required: true
    }
  },
  methods: {
    newEmoji ({ target }) {
      const column = (Math.floor(Math.random() * 11)) * -22
      const row = (Math.floor(Math.random() * 4)) * -22

      target.style.backgroundPosition = `${column}px ${row}px`
    }
  }
})
</script>

<template>
  <div :style="`background-image: url(${sources.emoji});`" class="vue3-discord-emojipicker__emojibutton filter grayscale hover:filter-none transform transition duration-200 cursor-pointer hover:scale-110" @mouseleave="newEmoji" @click="$emit('click', 'emoji')" />
</template>

<style scoped>
@tailwind base;
@tailwind components;
@tailwind utilities;
.emojibutton__active .vue3-discord-emojipicker__emojibutton {
  width: 25px;
}
.vue3-discord-emojipicker__emojibutton {
  background-position: -22px 0;
  background-size: 242px 88px;
  background-repeat: no-repeat;
  width: 23px;
  height: 22px;
  display: block;
}
</style>