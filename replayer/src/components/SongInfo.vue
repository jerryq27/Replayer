<template>
  
  <div id="details">
    <img class="album-art" v-bind:src="song.img" alt="">
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
.album-art {
  width: 250px;
  height: 250px;
}
</style>