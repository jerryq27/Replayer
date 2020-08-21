<template>
  
  <div>
      <p class="time">{{ updateCurrentTime() }} - {{ formatDuration }}</p>
      <div class="seekbar">
          <div
            v-bind:style="fill"
            class="seekbar-fill">
          </div>
          <div
            class="seekbar-thumb"
            v-bind:style="thumb"
            v-on:mousedown="startScrub"
            v-on:mouseup="endScrub">
          </div>
          <div class="seekbar-length"></div>
      </div>
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
            }
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
        startScrub: function(event) {
            console.log(event);
        },
        endScrub: function(event) {
            console.log(event);
        }
    }
}
</script>

<style>
.debug-box {
    border: solid red 1px;
}

.seekbar {
    height: 20px;
    width: 80%;
    margin: auto;
    position: relative;
}

.seekbar-fill {
    height: 90%;
    width: 0%;
    background: lightgreen;
    position: absolute;
}

.seekbar-length {
    height: 90%;
    width: 100%;
    background: gray;
}

.seekbar-thumb {
    height: 100%;
    width: 5%;
    border-radius: 50%;
    bottom: 5%;
    border: solid whitesmoke 1px;
    position: absolute;
    background: green;
}

.seekbar-thumb:hover {
    cursor: pointer;
}
</style>