<template>

  <q-card-section class="row">
    <span class="col-2 text-center">{{ updateCurrentTime() }}</span>
    <q-slider class="col-8"
        v-on:change="informSeek"
        v-model="currentTimeVal"
        v-bind:step="stepVal"
        v-bind:min="0"
        v-bind:max="time.duration"/>
    <span class="col-2 text-center">{{ formatDuration }}</span>
  </q-card-section>
  
</template>

<script>
export default {
    name: 'Seekbar',
    props: {
        time: Object,
        value: Number,
    },
    data: function() {
        return {
            stepVal: 0.1,
        }
    },
    computed: {
        currentTimeVal: {
            get: function() {
                return this.value;
            },
            set: function(newCurrentTimeVal) {
                this.$emit('input', newCurrentTimeVal);
            }
        },
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