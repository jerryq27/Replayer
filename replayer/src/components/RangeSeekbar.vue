<template>
  <div>
      <span class="time">{{ startTimeFormat }}</span>
      <div class="seekbar-container">
        <q-range
            color="green"
            label
            v-bind:left-label-value="startTimeFormat"
            v-bind:right-label-value="endTimeFormat"
            v-bind:min="0"
            v-bind:max="time.duration"
            v-model="range"
            v-bind:step="stepVal"
            v-on:change="updateRange"/>
      </div>
      <span class="time">{{ endTimeFormat }}</span>
  </div>
</template>

<script>
export default {
    name: 'RangeSeekbar',
    props: {
        time: Object,
    },
    data: function() {
        return {
            range: {
                min: 0,
                max: this.time.duration,
            },
            stepVal: 0.1,
        }
    },
    created: function() {
        // this.range.max = this.duration;
    },
    computed: {
        startTimeFormat: function() {
            return this.formatTime(this.range.min);
        },
        endTimeFormat: function() {
            return this.formatTime(this.range.max);
        }
    },
    methods: {
        formatTime: function(time) {
            let min = Math.floor(time/60);
            let sec = Math.floor(time%60);

            if(sec >= 60) sec = 0;
            
            if(sec < 10) return `${min}:0${sec}`;
            else return `${min}:${sec}`;
        },
        updateRange: function(value) {
            this.range.min = value.min;
            this.range.max = value.max;
            this.$emit('updateRange', value);
        }
    }
}
</script>

<style>

</style>