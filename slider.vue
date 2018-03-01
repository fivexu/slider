<template>
  <div class="wrapper" ref="wrapper"
       @touchstart="touchStart"
       @touchmove="touchMove"
       @touchend="touchEnd"
       @mouseenter="wrapperEnter"
       @mousemove="wrapperEnter"
       @mouseleave="_playTabs(currentIndex)">
    <div class="slide-wrapper" ref="slide">
      <slot></slot>
    </div>
    <span class="switch" v-if="!touched&&arrowsShow" @click="prev(currentIndex)"><</span>
    <span class="switch" v-if="!touched&&arrowsShow" @click="next(currentIndex)">></span>
    <div class="dots" v-if="dotsShow" ref="dots" @mouseenter="wrapperEnter">
      <div class="dot"
           :class="{ac:currentIndex===index}"
           ref="dot"
           v-if="sliderList"
           v-for="(item,index) in sliderList"
           @mouseenter="mouseEnter(index)"
           @mouseleave="_playTabs(currentIndex)">
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    props: {
      sliderList: {
        type: Array,
        default: []
      },
      time: {
        type: Number,
        default: 3000
      },
      autoPlay: {
        type: Boolean,
        default: true
      },
      dotsShow: {
        type: Boolean,
        default: true
      },
      arrowsShow: {
        type: Boolean,
        default: true
      },
      sliderType: {
        type: String,
        default: 'slide'
      },
      hoverStop: {
        type: Boolean,
        default: true
      },
      hidden: {
        type: Boolean,
        default: true
      }
    },
    data() {
      return {
        currentIndex: 0,
        dotsList: [],
        touched: false,
        width: 0
      }
    },
    created() {
      this.touch = {}
    },
    methods: {
      isPC() {
        let userAgentInfo = navigator.userAgent
        let Agents = ['Android', 'iPhone', 'SymbianOS', 'Windows Phone', 'iPad', 'iPod']
        let flag = true
        for (let v = 0; v < Agents.length; v++) {
          if (userAgentInfo.indexOf(Agents[v]) > 0) {
            flag = false
            break
          }
        }
        return flag
      },
      touchStart(ev) {
        if (!this.touched) {
          return
        }
        this.touch.initiated = true
        const touch = ev.touches[0]
        this.deltaX = 0
        this.touch.startX = touch.pageX
      },
      touchMove(ev) {
        if (!this.touch.initiated) {
          return
        }
        const touch = ev.touches[0]
        this.deltaX = touch.pageX - this.touch.startX
      },
      touchEnd() {
        this.touch.initiated = false
        if (this.deltaX > 0) {
          this.prev(this.currentIndex)
        }
        if (this.deltaX < 0) {
          this.next(this.currentIndex)
        }
      },
      _setTabsPosition(center = false) {
        if (!this.$refs.slide) {
          return
        }
        let children = this.$refs.slide.children
        for (let i = 0; i < children.length; i++) {
          children[i].className = center ? 'slide-3d' : 'slide'
          children[i].style.width = `${this.$refs.wrapper.offsetWidth}px`
          children[i].style.position = `absolute`
          children[i].style.left = 0
          children[i].style.right = 0
          children[i].style.zIndex = center ? 5 : `${children.length - i}`
        }
      },
      _getTabsType(type) {
        switch (type) {
          case 'slide':
            setTimeout(() => {
              this._setTabsSlide()
              this.slideTabs = true
            }, 20)
            break
          case 'fade':
            setTimeout(() => {
              this._setTabsFadeIn()
              this.fadeTabs = true
            }, 20)
            break
          case 'mosaic':
            setTimeout(() => {
              this._setTabsFadeIn()
              this.fadeTabs = true
              this.NoneTabs = true
            }, 20)
            break
          case '3d':
            setTimeout(() => {
              this._setTabs3D()
              this.ThreeDimensional = true
            }, 20)
            break
          default:
            setTimeout(() => {
              this._setTabsSlide()
              this.slideTabs = true
            }, 20)
        }
      },
      _setTabsType() {
        this.$emit('currentIndex', this.currentIndex)
        if (this.slideTabs) {
          this.$refs.slide.style.left = `${-this.$refs.slide.children[0].offsetWidth * this.currentIndex}px`
        }
        if (this.fadeTabs && !this.NoneTabs) {
          for (let i = 0; i < this.$refs.slide.children.length; i++) {
            this.$refs.slide.children[i].style.zIndex = 10
            this.$refs.slide.children[i].style.opacity = 0
          }
          this.$refs.slide.children[this.currentIndex].style.zIndex = 100
          this.$refs.slide.children[this.currentIndex].style.opacity = 1
        }
        if (this.fadeTabs && this.NoneTabs) {
          for (let i = 0; i < this.$refs.slide.children.length; i++) {
            this.$refs.slide.children[i].style.zIndex = 10
          }
          this.$refs.slide.children[this.currentIndex].style.zIndex = 100
        }
        if (this.ThreeDimensional) {
          let children = this.$refs.slide.children
          for (let i = 0; i < children.length; i++) {
            children[i].className = `slide-3d`
          }
          !children[this.currentIndex - 1] ? children[children.length - 1].className += ` last` : children[this.currentIndex - 1].className += ` last`
          !children[this.currentIndex + 1] ? children[0].className += ` next` : children[this.currentIndex + 1].className += ` next`
          children[this.currentIndex].className += ` current`
        }
      },
      _playTabs() {
        if (!this.autoPlay) {
          return
        }
        clearInterval(this.timer)
        this.timer = setInterval(() => {
          this.currentIndex++
          if (this.currentIndex >= this.$refs.slide.children.length) {
            this.currentIndex = 0
          }
          this._setTabsType()
        }, this.time)
      },
      _setTabsSlide() {
        if (this.$refs.slide.children.length <= 0) {
          return
        }
        let children = this.$refs.slide.children
        let width = 0
        for (let i = 0; i < children.length; i++) {
          width += this.$refs.wrapper.offsetWidth
          children[i].className = 'slide'
          children[i].style.width = `${this.$refs.wrapper.offsetWidth}px`
          if (this.dotsShow === true && this.$refs.wrapper.offsetWidth / this.$refs.dot.length - 10 <= 20) {
            this.$refs.dot[i].style.width = `${this.$refs.wrapper.offsetWidth / this.$refs.dot.length - 10}px`
            this.$refs.dot[i].style.height = `${this.$refs.wrapper.offsetWidth / this.$refs.dot.length - 10}px`
          }
        }
        this.$refs.slide.style.width = `${width}px`
      },
      _setTabs3D() {
        setTimeout(() => {
          this._setTabsPosition(true)
          let children = this.$refs.slide.children
          for (let i = 0; i < children.length; i++) {
            children[i].style.width = `${this.$refs.wrapper.offsetWidth / 2}px`
          }
          !children[this.currentIndex - 1] ? children[children.length - 1].className += ` last` : children[this.currentIndex - 1].className += ` last`
          !children[this.currentIndex + 1] ? children[0].className += ` next` : children[this.currentIndex + 1].className += ` next`
          children[this.currentIndex].className += ` current`
        }, 20)
      },
      _setTabsFadeIn() {
        this.$nextTick(() => {
          this._setTabsPosition()
        })
      },
      prev(index) {
        clearInterval(this.timer)
        index--
        if (index < 0) {
          index = this.$refs.slide.children.length - 1
        }
        this.currentIndex = index
        this._setTabsType()
        this._playTabs()
      },
      next(index) {
        clearInterval(this.timer)
        index++
        if (index >= this.$refs.slide.children.length) {
          index = 0
        }
        this.currentIndex = index
        this._setTabsType()
        this._playTabs()
      },
      mouseEnter(index) {
        clearInterval(this.timer)
        this.currentIndex = index
        this._setTabsType()
      },
      wrapperEnter() {
        if (this.hoverStop) {
          clearInterval(this.timer)
        }
      }
    },
    mounted() {
      this.$nextTick(() => {
        if (!this.sliderList.length) {
          return
        }
        if (!this.hidden) {
          this.$refs.wrapper.style.overflow = 'visible'
        }
        this._getTabsType(this.sliderType)
        this._playTabs(this.currentIndex)
      })
      this.isPC() ? this.touched = false : this.touched = true
      window.addEventListener('resize', () => {
        this._getTabsType(this.sliderType)
        this.isPC() ? this.touched = false : this.touched = true
      })
    },
    watch: {
      sliderList() {
        clearInterval(this.timer)
        this._getTabsType(this.sliderType)
        this._playTabs(this.currentIndex)
      }
    },
    destroyed() {
      clearInterval(this.timer)
    }
  }
</script>

<style scoped>
  .wrapper {
    cursor: pointer;
    width: 100%;
    height: 100%;
    position: relative;
    transform-style: preserve-3d;
    transform-origin: center;
    overflow: hidden;
  }
  .wrapper a {
    display: block;
    width: 100%;
    height: 100%;
  }
  .wrapper img {
    display: block;
    width: 100%;
    height: 100%;
  }
  .wrapper:hover .switch {
    opacity: 1;
  }
  .wrapper .slide-wrapper {
    transition: all 0.5s;
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    transform-style: preserve-3d;
    transform-origin: center;
  }
  .wrapper .slide-wrapper .slide {
    width: 100%;
    height: 100%;
    float: left;
    transition: opacity 0.5s;
    margin: 0 auto;
  }
  .wrapper .slide-wrapper .slide-3d {
    width: 100%;
    height: 100%;
    float: left;
    transition: all 0.5s ease;
    margin: 0 auto;
    opacity: 0;
    transform: scale(0.3);
  }
  .wrapper .slide-wrapper .slide-3d.last {
    opacity: 1;
    transform: translate3d(-60%, 0, 0) scale(0.8);
    z-index: 8 !important;
    filter: grayscale(0.3) blur(1px);
  }
  .wrapper .slide-wrapper .slide-3d.current {
    opacity: 1;
    transform: translate3d(0, 0, 0);
    z-index: 10 !important;
  }
  .wrapper .slide-wrapper .slide-3d.next {
    opacity: 1;
    transform: translate3d(60%, 0, 0) scale(0.8);
    z-index: 8 !important;
    filter: grayscale(0.3) blur(1px);
  }
  .wrapper .switch {
    user-select: none;
    position: absolute;
    top: 0;
    bottom: 0;
    margin: auto 0;
    font-size: 40px;
    opacity: 0;
    display: block;
    width: 40px;
    height: 60px;
    line-height: 65px;
    text-align: center;
    color: rgba(255, 255, 255, 0.5);
    cursor: pointer;
    transition: all 0.3s;
    transform: scaleY(2);
  }
  .wrapper .switch:hover {
    color: #fff;
  }
  .wrapper .switch:nth-of-type(1) {
    left: 50px;
  }
  .wrapper .switch:nth-of-type(2) {
    right: 50px;
  }
  .wrapper .dots {
    text-align: center;
    width: fit-content;
    height: 10px;
    position: absolute;
    left: 0;
    right: 0;
    bottom: 20px;
    margin: 0 auto;
    z-index: 50;
  }
  .wrapper .dots .dot {
    transition: all 0.3s;
    display: inline-block;
    width: 5px;
    height: 5px;
    margin: 0 5px;
    border: 1px solid #00ae66;
    border-radius: 5px;
    cursor: pointer;
  }
  .wrapper .dots .dot.ac {
    background-color: #00ae66;
    border-color: #00ae66;
    width: 30px;
  }
</style>