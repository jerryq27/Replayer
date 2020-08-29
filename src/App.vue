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
      </q-toolbar>
    </q-header>

    <q-page-container class="row justify-center">
      <q-card class="content-card col-xs-12 col-sm-8 col-md-4">

        <SongInfo v-bind:song="songs[currentIndex]"/>
        <q-card-actions>
          <q-file
            label="Add files.."
            rounded
            outlined
            multiple
            accept=".mp3, .m4a, .ogg, .wav"
            v-model="inputFiles"
            v-bind:max-files="maxFiles"
            v-on:input="addFile"
            v-on:rejected="rejectFile"/>
        </q-card-actions>

        <q-tabs narrow-indicator dense
          v-model="tab"
          align="justify">
            <q-tab name="playing-music" icon="play_circle_outlined"/>
            <q-tab name="creating-range" icon="loop"/>
        </q-tabs>
        <q-tab-panels animated v-model="tab">

          <q-tab-panel name="playing-music">
            <PlayerControls
              v-bind:isPlaying="isPlaying"
              v-bind:tab="tab"
              v-on:handleEvent="handleEvent"/>
            <Seekbar
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
              v-bind:time="time"
              v-bind:currentIndex="currentIndex"
              v-bind:useMillisecs="useMillisecs"
              v-on:updateRange="updateRange"/>
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
      maxFiles: 3,
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
          artist: 'KieLoKaz',
          title: 'Alte Herren Kielokaz',
          src: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/music/no_curator/KieLoKaz/Free_Ganymed/KieLoKaz_-_07_-_Alte_Herren_Kielokaz_ID_364.mp3',
          img: 'https://files.freemusicarchive.org/storage-freemusicarchive-org/images/albums/KieLoKaz_-_Free_Ganymed_-_20190912113845208.jpg',
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
      replayer.duration = this.duration;
      replayer.time.duration = this.duration;
    });
    this.player.addEventListener('timeupdate', function() {
      replayer.time.currentTime = this.currentTime;
      if(replayer.tab === 'creating-range') {
        replayer.replay()
      } 
    });

    // Fixes the sync between play/pause on mobile
    this.player.addEventListener('play', this.play);
    this.player.addEventListener('pause', this.pause);
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
    updateRange: function(value) {
      this.currentRange.min = value.min;
      this.currentRange.max = value.max;

      // To update the RangeSeekbar start time.
      this.player.currentTime = value.min;
    },
    addFile: function(files) {
      const musicMetadata = require('music-metadata-browser');

      for(let i = 0; i < files.length; i++) {
        this.defaults.title = files[i].name;
        musicMetadata.parseBlob(files[i]).then(metadata => {
          let srcUrl = URL.createObjectURL(files[i]);

          // Grab relavent metadata from the `common` object.
          const common = metadata.common;
          // src="data:<format>;base64, <your byte array in base64>">
          let imgData;
          if(common.picture) imgData = `data:${common.picture[0].format};base64, ${common.picture[0].data.toString('base64')}`;
          else imgData = this.defaults.img;

          this.songs.push({
            artist: common.artist || this.defaults.artist,
            title: common.title || this.defaults.title,
            src: srcUrl,
            img: imgData,
          });
        });
      }
    },
    rejectFile: function(rejectedFiles) {
      console.log(rejectedFiles);
      // this.$q.notify({
      //   type: 'negative',
      //   message: `${files.name} needs to be an .mp3, .m4a, or .ogg format!`
      // });
      let isFormatError = false;
      let isLengthError = false;

      let names = rejectedFiles.reduce((names, item) => {
        isLengthError = item.failedPropValidation === 'max-files';
        isFormatError = item.failedPropValidation === 'accept';
        return item.file.name + ',';
      }, '');
      names = `[ ${names.slice(0, names.length - 1)} ]`
      console.log(names);

      if(isLengthError) alert(`The following file(s): \n${names}\nonly a maximum of 3 files allowed!`);
      if(isFormatError) alert(`The following file(s): \n${names}\nneed to be an .mp3, .m4a, or .ogg format!`);
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
