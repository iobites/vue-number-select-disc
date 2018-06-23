<template>

  <svg class="number-disc" :width="width" :height="height" xmlns="http://www.w3.org/2000/svg">

    <defs>

      <filter id="dropshadow" height="130%">
        <feGaussianBlur in="SourceAlpha" stdDeviation="3"/> <!-- stdDeviation is how much to blur -->
        <feOffset dx="2" dy="2" result="offsetblur"/> <!-- how much to offset -->
        <feComponentTransfer>
          <feFuncA type="linear" slope="0.5"/> <!-- slope is the opacity of the shadow -->
        </feComponentTransfer>
        <feMerge>
          <feMergeNode/> <!-- this contains the offset blurred image -->
          <feMergeNode in="SourceGraphic"/> <!-- this contains the element that the filter is applied to -->
        </feMerge>
      </filter>

      <filter id="insetShadow" x="-50%" y="-50%" width="200%" height="200%">
        <feComponentTransfer in=SourceAlpha>
          <feFuncA type="table" tableValues="1 0" />
        </feComponentTransfer>
        <feGaussianBlur stdDeviation="3"/>
        <feOffset dx="5" dy="5" result="offsetblur"/>
        <feFlood flood-color="rgb(20, 0, 0)" result="color"/>
        <feComposite in2="offsetblur" operator="in"/>
        <feComposite in2="SourceAlpha" operator="in" />
        <feMerge>
          <feMergeNode in="SourceGraphic" />
          <feMergeNode />
        </feMerge>
      </filter>

      <linearGradient id="linearColors1" x1="0" y1="0" x2="1" y2="1">
        <stop offset="0%" stop-color="#01E400"></stop>
        <stop offset="100%" stop-color="#FEFF01"></stop>
      </linearGradient>

      <linearGradient id="colorGradient" x1="0" y1="0" x2="1" y2="1">
        <stop offset="0%" stop-color="#01E400"></stop>
        <stop offset="16%" stop-color="#FEFF01"></stop>
        <stop offset="32%" stop-color="#FF7E00"></stop>
        <stop offset="48%" stop-color="#FB0300"></stop>
        <stop offset="64%" stop-color="#9B004A"></stop>
        <stop offset="80%" stop-color="#7D0022"></stop>
        <stop offset="100%" stop-color="#01E400"></stop>
      </linearGradient>

    </defs>

    <g ref="svgElem">

      <circle class="outer-circle"
              :style="outerCircleStyle"
              :cx="centerX"
              :cy="centerY"
              :r="radius"
              @mousedown="mouseDown($event)"
              @touchstart="handleStart($event)" />
              <!-- stroke="url(#colorGradient)" -->
              <!-- stroke-width="5" /> -->

      <circle :class="innerCircleClasses"
              :style="innerCircleStyle"
              :cx="centerX"
              :cy="centerY"
              :r="innerCircleRadius"
              @touchstart="numberSelected"
              @click="numberSelected" />

      <text :x="textPosX"
            :y="centerY + 5"
            v-if="showText"
            class="svg-text"
            @click="numberSelected">
            {{formatedNumber}} {{unit}}
      </text>

    </g>

  </svg>

</template>

<style lang="stylus">

  .inner-circle
    fill: #CF0100
    filter: url(#insetShadow)

  .inner-circle:hover
    fill: #f70502 !important

  .inner-circle:active
    filter: url(#insetShadow)
    fill: #32c229 !important

  .inner-circle-disabled
    fill: blue
    filter: url(#insetShadow)

  .outer-circle
    fill: #2C3D51
    filter: url(#dropshadow)
    filter: brightness(85%)

  .svg-text
    -webkit-touch-callout: none /* iOS Safari */
    -webkit-user-select: none /* Safari */
    -khtml-user-select: none /* Konqueror HTML */
    -moz-user-select: none /* Firefox */
    -ms-user-select: none /* Internet Explorer/Edge */
    user-select: none /* Non-prefixed version, currently supported by Chrome and Opera */
    pointer-events: none

</style>

<script>

let lastAngle = 0
let ongoingTouch = null;

function copyTouch(touch) {
  return { identifier: touch.identifier, pageX: touch.pageX, pageY: touch.pageY };
}

export default {
  name: 'number-disc',

  props: {
    width: {
      type: [Number, String],
      default: 200
    },
    height: {
      type: [Number, String],
      default: 200
    },
    value: {
      type: [Number, String],
      default: 0
    },
    precision: {
      type: Number,
      default: 1
    },
    disabled: {
      type: Boolean,
      default: false
    },
    signed: {
      type: Boolean,
      default: false
    },
    showText: {
      type: Boolean,
      default: true
    },
    floating: {
      type: Boolean,
      default: true
    },
    fraction: {
      type: Number,
      default: 2,
      validator: value => value > 0 && value < 5
    },
    stepWidth: {
      type: Number,
      default: 1
    },
    strokeWidth: {
      type: Number,
      default: 20
    },
    innerColor: {
      type: String,
      default: '#CF0100'
    },
    outerColor: {
      type: String,
      default: '#2C3D51'
    },
    unit: {
      type: String,
      default: ''
    },
  },

  data () {
    return {
      radius: (this.width / 2 - 5),
      currentNumber: 0,
      currentAngle: 0,
    }
  },

  mounted () {
    this.currentNumber = this.value
    this.$refs.svgElem.addEventListener('mouseup',  this.removeMouseMoveListener)
    this.$refs.svgElem.addEventListener('touchend',  this.handleEnd)
    this.$refs.svgElem.addEventListener('touchcancel',  this.handleCancel)
  },

  computed: {
    innerCircleClasses: function () {
      return {
        'inner-circle': !this.disabled,
        'inner-circle-disabled': this.disabled
      }
    },

    textPosX () {
      let factor = 15
      if (this.currentNumber >= 100) {
        factor = 25
      }
      if (this.currentNumber >= 1000) {
        factor = 30
      }
      return this.centerX - factor
    },

    formatedNumber () {
      if (this.floating) {
        return this.currentNumber.toFixed(this.fraction)
      } else {
        return Math.floor(this.currentNumber)
      }
    },

    innerCircleRadius () {
      return this.radius - this.strokeWidth
    },

    innerCircleStyle () {
      return { fill: this.innerColor }
    },

    outerCircleStyle () {
      return {
        fill: this.outerColor,
        filter: 'url(#dropshadow)',
      }
    },

    outerCircleTransform () {
      return `rotate(${this.currentAngle})`
    },

    centerX () {
      return Math.floor(this.width/2)
    },

    centerY () {
      return Math.floor(this.height/2)
    },

    centerPoint () {
      return [Math.floor(this.width/2), Math.floor(this.height/2)]
    }
  },

  watch: {
    currentNumber () {
      this.$emit('input', this.formatedNumber)
    }
  },

  methods: {
    handleStart (evt) {
      evt.preventDefault();
      if (ongoingTouch === null) {
        ongoingTouch = copyTouch(evt.changedTouches[0])
        this.$refs.svgElem.addEventListener('touchmove', this.handleTouchMove)
      }
    },

    handleTouchMove(evt) {
      evt.preventDefault();
      const touches = evt.changedTouches;

      for (let i = 0; i < touches.length; i++) {
        if (ongoingTouch.identifier === touches[i].identifier) {
          this.updateCurrentNumber(touches[i].clientX,  touches[i].clientY)
          break;
        }
      }
    },

    handleEnd(evt) {
      evt.preventDefault();
      const touches = evt.changedTouches;

      if (ongoingTouch !== null) {
        for (let i = 0; i < touches.length; i++) {
          if (ongoingTouch.identifier === touches[i].identifier) {
            this.$refs.svgElem.removeEventListener('touchmove', this.handleTouchMove)
            ongoingTouch = null
            break;
          }
        }
      }
    },

    handleCancel(evt) {
      evt.preventDefault();
      const touches = evt.changedTouches;

      if (ongoingTouch !== null) {
        for (let i = 0; i < touches.length; i++) {
          if (ongoingTouch.identifier === touches[i].identifier) {
            this.$refs.svgElem.removeEventListener('touchmove', this.handleTouchMove)
            ongoingTouch = null
            break;
          }
        }
      }
    },

    mouseDown (evnt) {
      this.$refs.svgElem.addEventListener("mousemove", this.handleMouseMove)
    },

    removeMouseMoveListener (evnt) {
      this.$refs.svgElem.removeEventListener('mousemove', this.handleMouseMove)
    },

    updateCurrentNumber (x, y) {
      if (this.disabled) {
        return
      }
      const rect = this.$refs.svgElem.getBoundingClientRect()
      const centerX = rect.left + this.width / 2
      const centerY = rect.top + this.height / 2

      let dy = y - centerY
      const dx = x - centerX

      // Invert Y-axis
      // dy *= -1

      let theta = 0
      if (dx == 0) {
        if (dy > 0) {
          theta = 90
        } else {
          theta = 270
        }
      } else {
        theta = Math.atan2(dy, dx);
        theta *= 180 / Math.PI;
        if (theta < 0) {
          theta = 360 + theta;
        }
      }

      this.currentAngle = Math.floor(theta)
      const precision = this.precision - 1
      if (lastAngle < this.currentAngle - precision) {
        this.currentNumber += this.stepWidth
        lastAngle = this.currentAngle
      } else if (lastAngle > this.currentAngle + precision) {
        this.currentNumber -= this.stepWidth
        lastAngle = this.currentAngle
      }
      // Check boundaries
      if (!this.signed && this.currentNumber < 0) {
        this.currentNumber = 0
      }
    },

    handleMouseMove (evnt) {
        this.updateCurrentNumber(evnt.x, evnt.y)
    },

    numberSelected () {
      if (!this.disabled) {
        this.$emit('selected', this.formatedNumber)
      }
    },

    toDegrees (angle) {
      return angle * (180 / Math.PI);
    },

    toRadians (angle) {
      return angle * (Math.PI / 180);
    },

    getDistance (p1, p2) {
      return Math.abs(Math.sqrt(
        Math.pow(p2.x -p1.x, 2) +  Math.pow(p2.y - p1.y, 2)
      ))
    }
  }

}

</script>
