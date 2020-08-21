<template>
  
  <div>
      <p class="time">{{ updateCurrentTime() }} - {{ formatDuration }}</p>
      <b-input
        class="seekbar"
        type="range"
        v-model="computedTimeFromProps"
        v-bind:max="duration"
        v-on:change="seek">
      </b-input>
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
            fill: {
                width: '0%'
            },
            thumb: {
                left: '0%'
            },
            ctime: this.currentTime,
        }
    },
    computed: {
        formatDuration: function() {
            let min = Math.floor(this.duration/60);
            let sec = Math.floor(this.duration%60);

            if(sec >= 60)
                sec = 0;
            if(sec < 10)
                return `${min}:0${sec}`;
            else
                return `${min}:${sec}`;
        },
        computedTimeFromProps: function() {
            return this.ctime;
        }
    },
    methods: {
        updateCurrentTime: function() {
            let min = Math.floor(this.currentTime/60);
            let sec = Math.floor(this.currentTime%60);

            let position = this.currentTime * 100 / this.duration;
        
            if(position < 100) {
                this.fill.width = position + '%';
                this.thumb.left = (position - 2) + "%";
            }

            if(sec >= 60)
                sec = 0;
            if(sec < 10)
                return `${min}:0${sec}`;
            else
                return `${min}:${sec}`;
        },
        seek: function(value) {
            console.log(value);
            this.$emit('handleSeek', value);
        }
    },
}
</script>

<style>
.debug-box {
    border: solid red 1px;
}

.seekbar {
    width: 80%;
    margin: auto;
}

.seekbar.slider-track-low {
    background: lightblue;
}
</style>