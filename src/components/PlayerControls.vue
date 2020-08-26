<template>

  <q-card-actions align="center">

    <q-btn outline round icon="skip_previous"
      v-bind:color="tab === 'playing-music'? 'primary' : 'green'"
      v-on:click="informEvent('prev')"/>

    <q-btn outline round size="lg"
      v-bind:icon="isPlaying? 'pause' : 'play_arrow'"
      v-bind:color="tab === 'playing-music'? 'primary' : 'green'"
      v-on:click="informEvent('play-pause')"/>

    <q-btn outline round icon="skip_next"
      v-bind:color="tab === 'playing-music'? 'primary' : 'green'"
      v-on:click="informEvent('next')"/>

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