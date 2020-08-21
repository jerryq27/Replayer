<template>
  <div id="app">

    <SongInfo v-bind:song="songs[currentIndex]"/>
    <PlayerControls
      v-bind:isPlaying="isPlaying"
      v-on:handleEvent="handleEvent"/>
    <Seekbar
      v-bind:duration="duration"
      v-bind:currentTime="currentTime"
      v-bind:handleSeek="handleSeek"/>

  </div>
</template>

<script>
import SongInfo from './components/SongInfo';
import PlayerControls from './components/PlayerControls'
import Seekbar from './components/Seekbar'

export default {
  name: 'App',
  data: function() { 
    return {
      player: new Audio(),
      isPlaying: false,
      currentIndex: 0,
      duration: 0,
      currentTime: 0,
      songs: [
        {
          artist: 'Scott Holmes',
          title: 'Upbeat Party',
          src: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/music/no_curator/Scott_Holmes/Inspiring__Upbeat_Music/Scott_Holmes_-_04_-_Upbeat_Party.mp3',
          img: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/images/albums/Scott_Holmes_-_Inspiring__Upbeat_Music_-_2019090383732068.jpg',
        },
        {
          artist: 'Yung Kartz',
          title: 'Picture Perfect',
          src: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/music/no_curator/Yung_Kartz/August_2019/Yung_Kartz_-_05_-_Picture_Perfect.mp3',
          img: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/images/albums/Yung_Kartz_-_August_2019_-_20190831225103049.jpg',
        },
        {
          artist: 'KieLoKaz',
          title: 'Alte Herren Kielokaz',
          src: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/music/no_curator/KieLoKaz/Free_Ganymed/KieLoKaz_-_07_-_Alte_Herren_Kielokaz_ID_364.mp3',
          img: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/images/albums/KieLoKaz_-_Free_Ganymed_-_20190912113845208.jpg',
        },
      ]
    }
  },
  created: function() {
    this.player.src = this.songs[this.currentIndex].src;

    let replayer = this;
    this.player.addEventListener('loadeddata', function() {
      replayer.duration = this.duration;
    });
    this.player.addEventListener('timeupdate', function() {
      replayer.currentTime = this.currentTime;
    });
  },
  methods: {
    handleEvent: function(event) {
      if(event === 'play-pause') {
        this.isPlaying? this.pause() : this.play();
      }
      else if(event == 'prev') {
        this.prev();
      }
      else if(event == 'next') {
        this.next();
      }
    },
    handleSeek: function(value) {
      let shouldResume = this.isPlaying;
      if(this.isPlaying) {
        this.pause();
        this.isPlaying = false;
      }
      this.player.fastSeek(value);
      if(shouldResume) {
        this.play();
        this.isPlaying = true;
      }
    },
    play: function() {
      this.player.play()
      this.isPlaying = true;
    },
    pause: function() {
      this.player.pause()
      this.isPlaying = false;
    },
    prev: function() {
      if(this.isPlaying) {
        this.pause();
      }
      this.currentIndex--;
      if(this.currentIndex < 0) {
        this.currentIndex = this.songs.length - 1;
      }

      this.player.src = this.songs[this.currentIndex].src;
    },
    next: function() {
      if(this.isPlaying) {
        this.pause();
      }
      this.currentIndex++;
      if(this.currentIndex >= this.songs.length) {
        this.currentIndex = 0;
      }
      this.player.src = this.songs[this.currentIndex].src;
    }
  },
  components: {
    SongInfo,
    PlayerControls,
    Seekbar,
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
