<template>
  <div id="app">

    <SongInfo v-bind:song="songs[currentIndex]"/>
    <PlayerControls
      v-bind:isPlaying="isPlaying"
      v-bind:creatingRange="creatingRange"
      v-on:handleEvent="handleEvent"/>
    <RangeSeekbar
      v-if="creatingRange"
      v-bind:time="time"
      v-on:updateRange="updateRange"/>
    <Seekbar
      v-else
      v-bind:time="time"
      v-on:seekTo="seekTo"/>
  </div>
</template>

<script>
import SongInfo from './components/SongInfo';
import PlayerControls from './components/PlayerControls';
import Seekbar from './components/Seekbar';
import RangeSeekbar from './components/RangeSeekbar';

export default {
  name: 'App',
  data: function() { 
    return {
      player: new Audio(),
      isPlaying: false,
      currentIndex: 0,
      creatingRange: false,
      currentRange: {
        min: 0,
        max: 0,
      },
      ranges: [],
      time: {
        currentTime: 0,
        duration: 0,
      },
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
    this.player.volume = 0.4;

    let replayer = this;
    this.player.addEventListener('loadeddata', function() {
      replayer.duration = this.duration;
      replayer.time.duration = this.duration;
    });
    this.player.addEventListener('timeupdate', function() {
      if(replayer.creatingRange && this.currentTime >= replayer.currentRange.max) {
        this.currentTime = replayer.currentRange.min;
        replayer.time.currentTime = replayer.currentRange.min;
      }
      replayer.time.currentTime = this.currentTime;
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
      else if(event == 'create') {
        this.creatingRange = !this.creatingRange;
      }
    },
    play: function() {
      if(this.creatingRange)
        this.seekTo(this.currentRange.min);
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
    },
    seekTo: function(time) {
      // this.player.fastSeek(time);
      this.player.currentTime = time;
      this.time.currenttime = time;
    },
    updateRange: function(value) {
      console.log(value);
      this.currentRange.min = value.min;
      this.currentRange.max = value.max;
    },
  },
  components: {
    SongInfo,
    PlayerControls,
    Seekbar,
    RangeSeekbar,
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
