<template>
  <div id="controls">
      <button
        id="prev"
        v-on:click="informEvent('prev')">
          Prev
      </button>

      <button
        id="play-pause"
        v-on:click="informEvent('play-pause')">
          {{ isPlaying? "Pause" : "Play" }}
      </button>

      <button
        id="next"
        v-on:click="informEvent('next')">
          Next
      </button>
  </div>
</template>

<script>
export default {
    name: 'PlayerControls',
    props: {
        isPlaying: Boolean,
    },
    methods: {
        informEvent: function(event) {
            this.$emit('handleEvent', event);
        }
    }
}
</script>

<style>

</style>