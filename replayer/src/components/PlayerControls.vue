<template>

  <q-card-actions align="center">
    <button v-on:click="informEvent('prev')">
        Prev
    </button>

    <button v-on:click="informEvent('play-pause')">
        {{ isPlaying? "Pause" : "Play" }}
    </button>

    <button v-on:click="informEvent('next')">
        Next
    </button>
  </q-card-actions>

</template>

<script>
export default {
    name: 'PlayerControls',
    props: {
        isPlaying: Boolean,
        tab: String,
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