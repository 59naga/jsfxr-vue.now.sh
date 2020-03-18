<template>
  <div @click.exact="onClick">
    <global-events
      @keydown.enter="onClick"
      @keydown.space="onClick"
      @keydown.17="onSkip"
      @keyup.17="onSkipRelease"
    />
    <enabler />
    <p>{{ reading }}</p>
  </div>
</template>

<script lang="ts">
import 'jsfxr/jsfxr'
import { Howl } from 'howler'
import delay from 'delay'

import GlobalEvents from 'vue-global-events'
import Enabler from './enabler'

export default {
  components: {
    GlobalEvents,
    Enabler
  },
  data () {
    const message = 'Lorem ipsum dolor sit amet, consectetur adipisicing elit. Mollitia nobis suscipit eum repellat est molestias officiis adipisci quibusdam rerum natus impedit, voluptate aperiam voluptates necessitatibus ipsa quos, modi excepturi. Iure.'
    return { sound: null, message, delay: 50, i: 0, speed: 1, speedMin: 1, speedMax: 4, enable: false, status: null }
  },
  computed: {
    char () {
      return this.message[this.i] || ''
    },
    reading () {
      return this.message.slice(0, this.i)
    },
    isSkip () {
      return this.status === 'skip'
    }
  },
  methods: {
    createJsfxr () {
      const defaults = {
        waveType: 0,
        sustainTime: 0.05 + 0.05 * Math.random(),
        decayTime: 0.10 + 0.10 * Math.random(),
        startFrequency: 0.10 + 0.40 * Math.random(),
        slide: 0.10 + 0.10 * Math.random(),
        lpFilterCutoff: 1,
        masterVolume: 0.25
      }
      const data = { ...defaults }
      const keys = [
        'waveType',
        'attackTime',
        'sustainTime',
        'sustainPunch',
        'decayTime',
        'startFrequency',
        'minFrequency',
        'slide',
        'deltaSlide',
        'vibratoDepth',
        'vibratoSpeed',
        'changeAmount',
        'changeSpeed',
        'squareDuty',
        'dutySweep',
        'repeatSpeed',
        'phaserOffset',
        'phaserSweep',
        'lpFilterCutoff',
        'lpFilterCutoffSweep',
        'lpFilterResonance',
        'hpFilterCutoff',
        'hpFilterCutoffSweep',
        'masterVolume'
      ]
      const values = []
      keys.forEach(key => {
        values.push(data[key])
      })
      const sound = new Howl({ src: window.jsfxr(values) })
      this.sound = sound

      return new Promise(resolve => {
        sound.once('load', resolve)
      })
    },
    async onClick () {
      if (this.enable) {
        this.speed = this.speed === this.speedMax ? this.speedMin : this.speedMax
        return
      }
      await this.createJsfxr()

      this.i = 0
      this.speed = this.isSkip ? this.speedMax : this.speedMin
      this.enable = true
      this.readChar()
    },
    getCharType () {
      if (this.char.match(/[ 　]/)) {
        return 'space'
      }
      if (this.char.match(/[,、?？]/)) {
        return 'comma'
      }
      if (this.char.match(/[.。!！]/)) {
        return 'period'
      }
      return 'default'
    },
    async readChar () {
      if (!this.enable) {
        return
      }
      if (this.message.length <= this.i) {
        this.enable = false
        if (this.isSkip) {
          this.onClick()
        }
        return
      }

      if (this.getCharType() !== 'space') {
        if (this.speed > 1) {
          this.sound.rate(this.speed)
        } else {
          this.sound.rate(1)
        }
        this.sound.play()
      }

      this.i += this.speed

      await delay(this.delay)
      this.readChar()
    },
    onSkip () {
      this.status = 'skip'
      this.speed = this.speedMax
    },
    onSkipRelease () {
      this.status = null
      this.speed = this.speedMin
    }
  }
}
</script>

<style lang="scss" scoped>
@font-face {
  font-family: PixelMplus10-Regular;
  src: url('http://kuneoresearch.com/wp-content/themes/stinger5verkuneo/font/PixelMplus10-Regular.ttf') format("truetype");
}

div {
  font-family: PixelMplus10-Regular;
  padding: 1em;
  font-size: xx-large;
  color: white;
  background: grey;
}
</style>
