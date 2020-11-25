<template>
  <div class="container">
    <div
      v-if="items.length"
      class="flex-container"
      :style="{transform: `translateY(${Math.max(currentRow - 1, 0) * -290}px)`}"
    >
      <div
        v-for="(item, index) in items"
        :key="item.id"
        class="flex-item"
      >
        <div
          :style="{backgroundImage: `url('${item.imageUrl}')`}"
          :class="[{'selected': index === sIndex}, 'item']"
        >
          <div class="item-section">
            {{ item.section }}
          </div>

          <div class="item-textbox">
            <div><strong>{{ item.title }}</strong></div>
            <div>Date: {{ item.publishedDate }}</div>
          </div>
        </div>
      </div>
    </div>
    <Loader v-else />
  </div>
</template>

<script>
import {request} from './helpers/api'
import {remapItemCb} from './helpers/itemCreator'
import Loader from './components/Loader'

const ITEMS_PER_ROW = 3

const KEY_OFFSET_MAP = {
  37: -1, // LEFT
  38: -ITEMS_PER_ROW, // UP
  39: 1, // RIGHT
  40: ITEMS_PER_ROW // DOWN
}

export default {
  name: 'App',
  components: {Loader},
  data: () => ({
    items: [],
    sIndex: 0
  }),
  computed: {
    currentRow () {
      return Math.floor(this.sIndex / ITEMS_PER_ROW)
    }
  },
  methods: {
    handleKey (event) {
      event.preventDefault()

      const offset = KEY_OFFSET_MAP[event.keyCode]
      if (!offset) return

      this.sIndex = Math.min(Math.max(this.sIndex + offset, 0), this.items.length - 1)
    }
  },
  created () {
    window.addEventListener('keydown', this.handleKey)
  },
  mounted () {
    request(
      'GET',
      'https://api.nytimes.com/svc/mostpopular/v2/viewed/30.json?api-key=xLegS4tZufVlTomVdwcstvjJx0iGMDfn'
    )
      .then(e => {
        const response = JSON.parse(e.target.response)

        const remappedItems = response.results.map(remapItemCb)

        this.items =
          remappedItems
            .concat(remappedItems)
            .concat(remappedItems)
            .concat(remappedItems)
            .concat(remappedItems)
      })
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
}

.container {
  height: 100vh;
  position: relative;
  background-image: linear-gradient(
    to right bottom, rgba(6, 6, 6, 0.8), rgba(12, 12, 12, 0.8)
  );
}

.flex-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  padding-top: 40px;
  max-width: 1400px;
  min-width: 1400px;
  margin: auto;
  transition: all .2s ease-in-out;
}

.flex-item {
  width: 440px;
  height: 290px;
}

.item {
  position: relative;
  width: 90%;
  height: 90%;
}

.selected {
  transform: scale(1.1);
  transition: all .2s ease-in-out;
  border: 5px solid rgb(0, 195, 255);
}

.item-textbox {
  position: absolute;
  width: 100%;
  height: 30%;
  bottom: 0;
  background-color: rgba(0, 195, 255, 0.7);
}

.item-section {
  position: absolute;
  width: 100%;
  height: 10%;
  top: 0;
  background-color: rgba(255, 140, 0, 0.933);
  text-align: right;
}
</style>
