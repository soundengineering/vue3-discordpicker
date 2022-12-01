<template>
  <div style="height: calc(100% - 9px)">
    <header class="bg-grey-400 px-5 pt-2 pb-5 shadow-lg relative z-1">
      <div class="flex items-center justify-between w-full">
        <div class="flex items-center justify-between w-full bg-grey-600 rounded-md overflow-hidden pr-4">
          <input v-model="search" class="bg-grey-600 w-full text-sm py-2 px-3 text-white outline-none" placeholder="Find the perfect gif" />
          <img :src="sources.search" class="w-4" />
        </div>
      </div>
    </header>
    <div class="vue3-discordpicker__container flex">
      <div class="bg-grey-400 h-full w-full flex flex-col">
        <div class="overflow-auto relative p-4 h-full w-full">
          <div v-if="search !== '' && results && results.length" class="grid-container">
            <div
              v-for="(result, r) in results"
              :key="r"
              class="h-28 rounded-lg bg-cover text-white flex items-center justify-center relative font-semibold font-xl border-2 hover:border-blue transition duration-300 cursor-pointer group z-1"
              @click="send(result.media_formats)"
            >
              <img :src="renderSmallGif(result.media_formats)" />
            </div>
          </div>
          <div v-else-if="tags && tags.length" class="grid grid-cols-2 grid-flow-row auto-rows-auto	gap-4">
            <div
              v-for="(tag, t) in tags"
              :key="t"
              class="vue3-discord-emojipicker__gifimage h-28 rounded-lg bg-cover text-white flex items-center justify-center relative font-semibold font-xl border-2 hover:border-blue transition duration-300 cursor-pointer group z-1 overflow-hidden"
              :style="`background-image: url(${tag.image})`"
              @click="search = tag.searchterm"
            >
              <div class="vue3-discord-emojipicker__gifimage h-full absolute top-0 left-0 bg-black opacity-0 group-hover:opacity-70 transition duration-300 w-full -z-1">
              </div>
              {{ tag.searchterm }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    apiKey: {
      type: String,
      required: true
    },
    sources: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      search: null,
      results: [],
      tags: []
    }
  },
  mounted () {
    fetch(`https://g.tenor.com/v2/categories?key=${this.apiKey}`)
    .then(res => res.json())
    .then(({ tags }) => this.tags = tags)
  },
  watch: {
    search: 'onSearch',
  },
  methods: {
    onSearch (key) {
      this.get(`search?q=${key}&limit=32`, 'results')
    },
    renderSmallGif ({ tinygif }) {
      return tinygif.url
    },
    renderHugeGif ({ mediumgif }) {
      return mediumgif.url
    },
    get (query, key) {
      fetch(`https://g.tenor.com/v2/${query}&key=${this.apiKey}`)
      .then(res => res.json())
      .then(data => this[key] = data[key])
    },
    send (url) {
      this.$emit('send', { url: this.renderHugeGif(url), send: true, type: 'gif' })
      this.search = null
    }
  }
}
</script>

<style scoped>
@tailwind base;
@tailwind components;
@tailwind utilities;

#vue3-discordpicker {
  scroll-behavior: smooth;
}
.grid-container {
  columns: 2 170px !important;
  column-gap: 10px !important;
  padding: 0px !important;
}
.grid-container div {
  width: 100% !important;
  height: 100% !important;
  margin-bottom: 10px !important;
  display: inline-block !important;
}
.border-2 {
  border-color: transparent;
}
.bg-cover {
  background-position: center !important;
  background-repeat: no-repeat !important;
}
.rounded-lg, .rounded-lg img {
  border-radius: 0.5em !important;
}
.bg-black {
  top: 0px !important;
  border-radius: 0.5em !important;
}
.border-blue {
  border-radius: 0.5em !important;
}
.vue3-discordpicker__container {
  height: calc(100% - 95px);
}
@media (max-width: 768px) {
  .vue3-discordpicker__container {
    height: 100%;
  }
}
.vue3-discordpicker__container ::-webkit-scrollbar {
  width: 10px;
}
.vue3-discordpicker__container ::-webkit-scrollbar-track {
  background: #2F3136;
  right: 5px;
}
.vue3-discordpicker__container ::-webkit-scrollbar-thumb {
  border-radius: 100px;
  background: #212224;
}
.z-1 {
  z-index: 1;
}
</style>