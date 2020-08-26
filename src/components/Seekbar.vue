<template>

  <q-card-section class="row">
    <span class="col-xs-1 col-sm-2 text-center q-pr-sm">{{ updateCurrentTime() }}</span>
    <q-slider class="col-xs-10 col-sm-8"
        v-on:change="informSeek"
        v-bind:value="time.currentTime"
        v-bind:step="stepVal"
        v-bind:min="0"
        v-bind:max="time.duration"/>
    <span class="col-xs-1 col-sm-2 text-center q-pl-sm">{{ formatDuration }}</span>
  </q-card-section>
  
</template>

<script>
export default {
    name: 'Seekbar',
    props: {
        time: Object,
    },
    data: function() {
        return {
            stepVal: 0.1,
        }
    },
    computed: {
        formatDuration: function() {
            let min = Math.floor(this.time.duration/60);
            let sec = Math.floor(this.time.duration%60);

            if(sec >= 60) sec = 0;
            
            if(sec < 10) return `${min}:0${sec}`;
            else return `${min}:${sec}`;
        }
    },
    methods: {
        updateCurrentTime: function() {
            let min = Math.floor(this.time.currentTime/60);
            let sec = Math.floor(this.time.currentTime%60);

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

</style>