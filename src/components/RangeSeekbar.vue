<template>

    <q-card-section>
        <div class="row">
            <span class="col-xs-1 col-sm-2 text-center q-pr-md">{{ startTimeFormat }}</span>
            <q-range class="col-xs-10 col-sm-8"
                color="green"
                label
                v-bind:left-label-value="leftHandle"
                v-bind:right-label-value="rightHandle"
                v-bind:min="0"
                v-bind:max="time.duration"
                v-model="range"
                v-bind:step="stepVal"
                v-on:change="updateRange"/>
            <span class="col-xs-1 col-sm-2 text-center q-pl-md">{{ endTimeFormat }}</span>
        </div>
    </q-card-section>

</template>

<script>
export default {
    name: 'RangeSeekbar',
    props: {
        time: Object,
        currentIndex: Number,
        useMillisecs: Boolean,
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
    computed: {
        startTimeFormat: function() {
            // Updates time when playing range.. need to rework this.
            return this.formatTime(this.time.currentTime);
        },
        endTimeFormat: function() {
            return this.formatTime(this.range.max);
        },
        leftHandle: function() {
            return this.useMillisecs? this.formatPreciseTime(this.range.min) : this.formatTime(this.range.min);
        },
        rightHandle: function() {
            return this.useMillisecs? this.formatPreciseTime(this.range.max) : this.formatTime(this.range.max);
        }
    },
    watch: {
        'time.duration': {
            handler: function(newDuration, oldDuration) {
                console.log(`Duration updatat: ${oldDuration} -> ${newDuration}`);
                this.range.min = 0;
                this.range.max = newDuration;
            }
        },
        useMillisecs: {
            handler: function(usingMs) {
                this.stepVal = usingMs? 0.001 : 0.1;
            }
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
        formatPreciseTime(time) {
            let timeString = this.formatTime(time)
            let millisecs = Math.floor((time%1) * 1000)

            if(millisecs < 100) return `${timeString}.0${millisecs}`
            else return `${timeString}.${millisecs}`;
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