<template>

  <q-card-actions>
    <button
      v-on:click="informEvent('prev')"
      v-bind:disabled="creatingRange">
        Prev
    </button>

    <button
      v-on:click="informEvent('play-pause')">
        {{ isPlaying? "Pause" : "Play" }}
    </button>

    <button
      v-on:click="informEvent('next')"
      v-bind:disabled="creatingRange">
        Next
    </button>

    <button 
      v-on:click="informEvent('create')">
        {{ creatingRange? "Exit" : "Create Range" }}
    </button>
  </q-card-actions>

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