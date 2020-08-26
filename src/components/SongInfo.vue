<template>

  <q-img v-bind:src="song.img">
    <div class="absolute-bottom">
      <div class="text-h6">{{ song.title }}</div>
      <div class="text-subtitle2">{{ song.artist }}</div>
    </div>
  </q-img>
  
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