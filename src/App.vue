<template>

  <div id="app">
  <q-layout view="hhh lpR fFf">

    <q-header elevated class="bg-primary text-white">
      <q-toolbar>
        <q-toolbar-title>
          <!-- <q-avatar>
            <img src="https://cdn.quasar.dev/logo/svg/quasar-logo.svg">
          </q-avatar> -->
          Replayer
        </q-toolbar-title>
        
        <q-btn flat dense icon="more_vert">

          <q-menu>
            <q-list class="text-no-wrap">
              <q-item clickable v-close-popup>
                <q-item-section avatar>
                  <q-icon name="movie"/>
                </q-item-section>
                <q-item-section v-on:click="openVideo">"Routines" feature idea</q-item-section>
              </q-item>
            </q-list>
          </q-menu>

        </q-btn>

      </q-toolbar>
    </q-header>

    <q-page-container class="row justify-center">
      <q-card class="content-card col-xs-12 col-sm-8 col-md-4">

        <SongInfo v-touch-swipe.mouse.left.right="handleAlbumSwipe" v-bind:song="songs[currentIndex]"/>
        <q-card-actions>
          <q-file
            label="Add file.."
            rounded
            outlined
            clearable
            accept=".mp3, .m4a, .ogg, .wav"
            v-model="inputFiles"
            v-on:input="addFile"
            v-on:rejected="rejectFile"/>
        </q-card-actions>

        <q-tabs narrow-indicator dense
          v-touch-swipe.mouse.left.right="handleTabSwipe"
          v-model="tab"
          align="justify">
            <q-tab name="playing-music" icon="play_circle_outlined"/>
            <q-tab name="creating-range" icon="loop"/>
        </q-tabs>
        <q-tab-panels animated v-model="tab" v-touch-swipe.mouse.left.right="handleTabSwipe">

          <q-tab-panel name="playing-music">
            <PlayerControls
              v-bind:isPlaying="isPlaying"
              v-bind:tab="tab"
              v-on:handleEvent="handleEvent"/>
            <Seekbar
              v-model="time.currentTime"
              v-bind:time="time"
              v-on:seekTo="seekTo"/>
          </q-tab-panel>

          <q-tab-panel name="creating-range">
            <div class="row">
              <PlayerControls class="col-xs-11"
                v-bind:disabled="disableButtons"
                v-bind:isPlaying="isPlaying"
                v-bind:tab="tab"
                v-on:handleEvent="handleEvent"/>
              <q-btn flat
                class="col-xs-1"
                icon="more_vert"
                color="green">
                  <q-menu>

                    <q-item >
                      <div class="row no-wrap q-pa-xs">
                        <q-toggle v-close-popup color="green" v-model="useMillisecs" label="Use Milliseconds"/>
                      </div>
                    </q-item>
                    
                    <q-item clickable>
                      <q-item-section side>
                        <q-icon name="keyboard_arrow_left" />
                      </q-item-section>
                      <q-item-section>Replay gap</q-item-section>
                      <q-menu anchor="top left" self="top right">
                        <q-item>
                          <q-option-group
                            color="green"
                            type="radio"
                            v-bind:options="gapValues"
                            v-model="replayGap"/>
                        </q-item>
                      </q-menu>
                    </q-item>
                    
                    <!-- <q-item>
                    </q-item>  -->
                  
                  </q-menu>
              </q-btn>
            </div>
            <RangeSeekbar
              v-model="currentRange"
              v-bind:time="time"
              v-bind:currentIndex="currentIndex"
              v-bind:useMillisecs="useMillisecs"/>
          </q-tab-panel>

        </q-tab-panels>

      </q-card>
    </q-page-container>

  </q-layout>
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
      videoUrl: 'http://i8.ae/7E9tw',
      player: new Audio(),
      isPlaying: false,
      useMillisecs: false,
      disableButtons: false,
      replayGap: 0,
      gapValues: [
        { label: 'No gap', value: 0 },
        { label: '3 seconds', value: 3 },
        { label: '5 seconds', value: 5 },
      ],
      currentIndex: 0,
      tab: 'playing-music',
      currentRange: {
        min: 0,
        max: 0,
      },
      ranges: [],
      time: {
        currentTime: 0,
        duration: 0,
      },
      inputFiles: [],
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
          artist: 'Broke For Free',
          title: 'Something Elated',
          src: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/music/WFMU/Broke_For_Free/Something_EP/Broke_For_Free_-_05_-_Something_Elated.mp3',
          img: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/images/albums/Broke_For_Free_-_Something_EP_-_20110708115706855.jpg',
        },
      ],
      defaults: {
        artist: 'Unknown',
        title: 'Unknown',
        img: 'https://upload.wikimedia.org/wikipedia/commons/3/3c/No-album-art.png'
      }
    }
  },
  created: function() {
    this.player.src = this.songs[this.currentIndex].src;
    this.player.volume = 0.4;

    let replayer = this;
    this.player.addEventListener('loadeddata', function() {
      replayer.time.duration = this.duration;
      replayer.currentRange.max = this.duration;
    });
    this.player.addEventListener('timeupdate', function() {
      replayer.time.currentTime = this.currentTime;
      if(replayer.tab === 'creating-range') {
        replayer.replay()
      } 
    });
    this.player.addEventListener('ended', function() {
      if(replayer.tab === 'creating-range') {
        replayer.play();
      }
      else {
        setTimeout(() => {
          replayer.next();
          replayer.play();
        }, 250);
      }
    });

    // Fixes the sync between play/pause on mobile
    this.player.addEventListener('play', this.play);
    this.player.addEventListener('pause', this.pause);
  },
  methods: {
    openVideo: function() {
      window.open(this.videoUrl, '_blank').focus();
    },
    handleTabSwipe: function(event) {
      if(event.direction === 'right' && this.tab !== 'playing-music') {
        this.tab = 'playing-music';
      }
      else if(event.direction === 'left' && this.tab !== 'creating-range') {
        this.tab = 'creating-range';
      }
    },
    handleAlbumSwipe: function(event) {
      if(event.direction === 'right') this.prev();
      else if(event.direction === 'left') this.next();
    },
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
    play: function() {
      if(this.tab === 'creating-range')
        this.seekTo(this.currentRange.min);
      this.player.play()
      this.isPlaying = true;
    },
    replay: function() {
        if(this.time.currentTime >= this.currentRange.max) {
          this.time.currentTime = this.player.currentTime = this.currentRange.min;
          if(this.replayGap != 0) {
            this.pause();
            this.disableButtons = true;
            setTimeout(() => {
              this.disableButtons = false;
              this.play();
            }, this.replayGap * 1000);
          }
        }
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

      if(this.tab === 'creating-range') {
        this.currentRange.min = 0;
        this.currentRange.max = this.time.duration;
      }
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

      if(this.tab === 'creating-range') {
        this.currentRange.min = 0;
        this.currentRange.max = this.time.duration;
      }
    },
    seekTo: function(time) {
      // this.player.fastSeek(time);
      this.player.currentTime = time;
      this.time.currentTime = time;
    },
    addFile: function(file) {
      const musicMetadata = require('music-metadata-browser');

      this.defaults.title = file.name;
      musicMetadata.parseBlob(file).then(metadata => {
        let srcUrl = URL.createObjectURL(file);

        // Grab relavent metadata from the `common` object.
        const common = metadata.common;
        // src="data:<format>;base64, <your byte array in base64>">
        let imgData;
        if(common.picture) imgData = `data:${common.picture[0].format};base64, ${common.picture[0].data.toString('base64')}`;
        else imgData = this.defaults.img;

        const newSong = {
          artist: common.artist || this.defaults.artist,
          title: common.title || this.defaults.title,
          src: srcUrl,
          img: imgData,
        };

        this.songs.push(newSong);
        this.currentIndex = this.songs.indexOf(newSong);
        this.player.src = this.songs[this.currentIndex].src;

        this.$q.notify({
          color: 'primary',
          message: `"${file.name}" has been added!`,
          actions: [{ icon: 'close', color: 'white' }],
        });
      });
    },
    rejectFile: function(info) {
      console.log(info);
      const rejectedFile = info[0].file.name;
      
      this.$q.notify({
        type: 'negative',
        message: `"${rejectedFile}" needs to be an .mp3, .m4a, or .ogg file!`,
        actions: [{ icon: 'close', color: 'white' }],
      });
    }
  },
  watch: {
    'currentRange.min': {
      handler: function(newMin) {
        this.time.currentTime = newMin;
      }
    }
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
.content-card {
  width: 20%;
  height:40%;
}
.debug-box { border: red solid 1px; }
</style>
