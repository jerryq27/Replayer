<template>
  
  <div>
      <span class="time">{{ updateCurrentTime() }}</span>
      <div class="seekbar-container">
        <q-slider
            v-on:change="informSeek"
            v-bind:value="currentTime"
            v-bind:step="stepVal"
            v-bind:min="0"
            v-bind:max="duration"/>
      </div>
      <span class="time">{{ formatDuration }}</span>
  </div>

</template>

<script>
export default {
    name: 'Seekbar',
    props: {
        duration: Number,
        currentTime: Number,
    },
    data: function() {
        return {
            durationString: '',
            currentTimeString: '',
            stepVal: 0.1,
        }
    },
    computed: {
        formatDuration: function() {
            let min = Math.floor(this.duration/60);
            let sec = Math.floor(this.duration%60);

            if(sec >= 60) sec = 0;
            
            if(sec < 10) return `${min}:0${sec}`;
            else return `${min}:${sec}`;
        }
    },
    methods: {
        updateCurrentTime: function() {
            let min = Math.floor(this.currentTime/60);
            let sec = Math.floor(this.currentTime%60);

            if(sec >= 60)
                sec = 0;
            if(sec < 10)
                return `${min}:0${sec}`;
            else
                return `${min}:${sec}`;
        },
        informSeek: function(time) {
            this.$emit('seekTo', time);
        }
    }
}
</script>

<style>
.debug-box {
    border: solid red 1px;
}

.seekbar-container {
    width: 80%;
    margin: auto;
    display: inline-block;
}


.time {
    display: inline-block;
    position: relative;
    bottom: 15px;
    margin: 0 15px;
}
</style>