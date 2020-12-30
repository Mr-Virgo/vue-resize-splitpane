<template>
  <div :style="{ cursor, userSelect}" class="vue-splitter-container clearfix" @mouseup="onMouseUp" @mousemove="onMouseMove">

    <pane class="splitter-pane splitter-paneL" :split="split" :style="{ [type]: getSize(percent)}">
      <slot name="paneL"></slot>
    </pane>

    <resizer :className="className" :style="{ [resizeType]: getSize(percent)}" :split="split" @mousedown.native="onMouseDown" @click.native="onClick"></resizer>

    <pane class="splitter-pane splitter-paneR" :split="split" :style="{ [type]: getSize(percent, true)}">
      <slot name="paneR"></slot>
    </pane>
    <div class="vue-splitter-container-mask" v-if="active"></div>
  </div>
</template>

<script>
  import Resizer from './resizer.vue'
  import Pane from './pane.vue'
  let pixelReg = /^\d+(\.\d+)?px$/
  let percentReg = /^\d+(\.\d+)?%?$/
  export default {
    name: 'splitPane',
    components: { Resizer, Pane },
    props: {
      minSize: {
        type: String | Number,
        default: 10,
        validator: function (value) {
          return pixelReg.test(value) || percentReg.test(value)
        }
      },
      defaultSize: {
        type: String | Number,
        default: 50,
        validator: function (value) {
          return pixelReg.test(value) || percentReg.test(value)
        }
      },
      split: {
        validator(value) {
          return ['vertical', 'horizontal'].indexOf(value) >= 0
        },
        required: true
      },
      className: String
    },
    computed: {
      userSelect() {
        return this.active ? 'none' : ''
      },
      cursor() {
        return this.active ? (this.split === 'vertical' ? 'col-resize' : 'row-resize') : ''
      }
    },
    watch: {
      defaultSize(newValue,oldValue){
        this.percent = newValue
      }
    },
    data() {
      return {
        active: false,
        hasMoved: false,
        height: null,
        percent: this.defaultSize,
        type: this.split === 'vertical' ? 'width' : 'height',
        resizeType: this.split === 'vertical' ? 'left' : 'top'
      }
    },
    methods: {
      onClick() {
        if (!this.hasMoved) {
          this.percent = 50
          this.$emit('resize', this.percent)
        }
      },
      onMouseDown() {
        this.active = true
        this.hasMoved = false
      },
      onMouseUp() {
        this.active = false
      },
      onMouseMove(e) {
        if (e.buttons === 0 || e.which === 0) {
          this.active = false
        }

        if (this.active) {
          let offset = 0
          let target = e.currentTarget
          if (this.split === 'vertical') {
            while (target) {
              offset += target.offsetLeft
              target = target.offsetParent
            }
          } else {
            while (target) {
              offset += target.offsetTop
              target = target.offsetParent
            }
          }

          const currentPage = this.split === 'vertical' ? e.pageX : e.pageY
          const targetOffset = this.split === 'vertical' ? e.currentTarget.offsetWidth : e.currentTarget.offsetHeight
          const percent = Math.floor(((currentPage - offset) / targetOffset) * 10000) / 100
          let minPercent = this.minSize
          if (pixelReg.test(minPercent)) {
            minPercent = Math.floor((minPercent / targetOffset) * 10000) / 100
          } else if (percentReg.test(minPercent)) {
            minPercent = (minPercent + '').replace('%', '')
          }
          if (percent > minPercent && percent < 100 - minPercent) {
            this.percent = percent
          }

          this.$emit('resize', this.percent)
          this.hasMoved = true
        }
      },
      getSize(size, right) {
        let result = size
        if (percentReg.test(size)) {
          result = (size + '').replace('%', '') + '%'
        }
        if (right) {
          result = `calc(100% - ${result})`
        }
        return result
      }
    }
  }
</script>

<style scoped>
.clearfix:after {
  visibility: hidden;
  display: block;
  font-size: 0;
  content: " ";
  clear: both;
  height: 0;
}

.vue-splitter-container {
  height: 100%;
  position: relative;
}

.vue-splitter-container-mask {
  z-index: 9999;
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}
</style>
