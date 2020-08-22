<template>
  
  <div>
      <p id="time">{{ updateCurrentTime() }} - {{ formatDuration }}</p>
      <div id="seekbar">
          <div id="length"></div>
          <div id="fill" v-bind:style="styles.fill"></div>
          <div id="thumb"></div>
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
            styles: {
                fill: {
                    background: 'lightgreen',
                    position: 'relative',
                    height: '5px',
                    width: '0px'
                }
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

            this.styles.fill.width = Math.floor((this.currentTime * 100) / this.duration)+ '%';
            console.log(this.currentTime);

            if(sec >= 60)
                sec = 0;
            if(sec < 10)
                return `${min}:0${sec}`;
            else
                return `${min}:${sec}`;
        }
    }
}
</script>

<style>
#length {
    background: gray;
    padding: 5px;
}

#thumb {
    background: green;
}
</style>