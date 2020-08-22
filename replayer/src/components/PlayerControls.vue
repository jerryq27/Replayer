<template>
  <div id="controls">
      <button
        id="prev"
        v-on:click="informEvent('prev')"
        v-bind:disabled="creatingRange">
          Prev
      </button>

      <button
        id="play-pause"
        v-on:click="informEvent('play-pause')">
          {{ isPlaying? "Pause" : "Play" }}
      </button>

      <button
        id="next"
        v-on:click="informEvent('next')"
        v-bind:disabled="creatingRange">
          Next
      </button>

      <button 
        id="create"
        v-on:click="informEvent('create')">
          {{ creatingRange? "Exit" : "Create Range" }}
      </button>
  </div>
</template>

<script>
export default {
    name: 'PlayerControls',
    props: {
        isPlaying: Boolean,
        creatingRange: Boolean,
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