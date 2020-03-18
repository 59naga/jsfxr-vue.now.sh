<template>
  <div class="overlay" :class="enable ? 'active' : ''" @click="onClick">
    <global-events @keydown.enter="onClick" @keydown.space="onClick" />
    ▶
  </div>
</template>

<script lang="ts">
import GlobalEvents from 'vue-global-events'

export default {
  components: {
    GlobalEvents
  },
  data () {
    return { enable: false }
  },
  methods: {
    onClick () {
      this.enable = true
    }
  }
}
</script>

<style lang="scss" scoped>
.overlay {
  cursor:pointer;

  z-index: 100;
  position: fixed;
  padding: 0;
  margin: 0;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  text-align: center;
  font-size: 80vh;
  line-height: 100vh;
  background: rgba(0, 0, 0, 0.5);
  color: black;

  &.active {
    display: none;
  }
}
@media (orientation: portrait) {
  .overlay {
    font-size: 80vw;
  }
}
</style>
