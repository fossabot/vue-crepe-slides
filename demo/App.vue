<template>
  <div id="app">
    <h1>Crepe Slides for Vue</h1>
    <div class="content">
      <crepe-slides :slides="slides" :width="width" :can-play-video="videoSupport"
        :aspect-ratio="16 / 9" :show-indicators="true"
        :active.sync="currentIndex"></crepe-slides>
      <div class="control">
        <span class="button" @click="currentIndex--">-</span>
        <span class="active">{{ currentIndex }}</span>
        <span class="button" @click="currentIndex++">+</span>
      </div>
    </div>
    <div class="footer">Powered by Kyle</div>
  </div>
</template>

<script>
import {throttle, clamp, includes} from 'lodash'
import CrepeSlides from '../lib/index'
import {VideoSupportState} from '../lib/index'
import slidesData from './slides.json'

export default {
  name: 'app',
  components: {
    CrepeSlides
  },
  data () {
    return {
      slides: slidesData,
      width: 320,
      currentIndex: 0
    }
  },
  computed: {
    videoSupport() {
      let ua = navigator.userAgent
      let isMobile = includes(ua, 'Mobile')
      let isIphone = includes(ua, 'iPhone')
      let isGoodIos = isIphone && (includes(ua, 'OS 10_') ||  includes(ua, 'OS 11_'))
      if (isMobile) {
        if (isGoodIos) {
          return VideoSupportState.FULL
        }
        else if (isIphone) {
          return VideoSupportState.SEMI
        }
        return VideoSupportState.NONE
      }
      return VideoSupportState.FULL
    }
  },
  methods: {
    handleWindowResize() {
      let width = document.documentElement.clientWidth
      this.width = clamp(width, 320, 540)
    }
  },
  mounted() {
    this.handleWindowResize()
    this.handleWindowResizeTrottled = throttle(this.handleWindowResize, 100)
    window.addEventListener('resize', this.handleWindowResizeTrottled)
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.handleWindowResizeTrottled)
  }
}
</script>

<style>
@import url(https://fonts.googleapis.com/css?family=Courgette);
</style>

<style lang="less">
html,
body {
  height: 100%;
}

body {
  margin: 0;
  font-family: 'Courgette', cursive;
}
h1 {
  text-align: center;
  color: orangered;
  text-shadow: 1px 1px 0 orange;
}

.content {
  font-family: sans-serif;
}

.control {
  @border-color: #333;
  border: 1px solid @border-color;
  border-radius: 3px;
  display: flex;
  width: 88px;
  margin: 20px auto;
  text-align: center;

  .button {
    cursor: default;
    user-select: none;
    flex: 0 0 20px;
    &:first-child {
      border-right: 1px solid @border-color;
    }
    &:last-child {
      border-left: 1px solid @border-color;
    }
  }
  .active {
    flex: 1 0 30px;
  }
}

.crepe-slides {
  margin: 0 auto;
}

#app {
  height: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-direction: column;
}

.footer {
  margin: 1em 0;
  color: #ccc;
  font-size: 12px;
}
</style>
