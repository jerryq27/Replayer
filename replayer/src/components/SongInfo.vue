<template>
  
  <div id="details">
    <img v-bind:src="song.img" alt="">
    <p id="artist-song">{{ song.artist }} - {{ song.title }}</p>
  </div>

</template>

<script>
export default {
    name: 'SongInfo',
    props: {
        song: Object,
    }
}
</script>

<style>

</style>