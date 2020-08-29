<template>

    <q-card-section>
        <div class="row">
            <span class="col-2 text-center">{{ startTimeFormat }}</span>
            <q-range class="col-8"
                color="green"
                label
                v-model="currentRangeVal"
                v-bind:left-label-value="leftHandle"
                v-bind:right-label-value="rightHandle"
                v-bind:min="0"
                v-bind:max="time.duration"
                v-bind:step="stepVal"/>
            <span class="col-2 text-center">{{ endTimeFormat }}</span>
        </div>
    </q-card-section>

</template>

<script>
export default {
    name: 'RangeSeekbar',
    props: {
        time: Object,
        value: Object,
        currentIndex: Number,
        useMillisecs: Boolean,
    },
    data: function() {
        return {
            stepVal: 0.1,
        }
    },
    computed: {
        currentRangeVal: {
            get: function() {
                return this.value;
            },
            set: function(newRangeVal) {
                this.$emit('input', newRangeVal)
            }
        },
        startTimeFormat: function() {
            // Updates time when playing range.. need to rework this.
            return this.formatTime(this.time.currentTime);
        },
        endTimeFormat: function() {
            return this.formatTime(this.value.max);
        },
        leftHandle: function() {
            return this.useMillisecs? this.formatPreciseTime(this.value.min) : this.formatTime(this.value.min);
        },
        rightHandle: function() {
            return this.useMillisecs? this.formatPreciseTime(this.value.max) : this.formatTime(this.value.max);
        }
    },
    watch: {
        // 'time.duration': {
        //     handler: function(newDuration, oldDuration) {
        //         console.log(`Duration updatat: ${oldDuration} -> ${newDuration}`);
        //         this.value.min = 0;
        //         this.value.max = newDuration;
        //     }
        // },
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
            this.value.min = value.min;
            this.value.max = value.max;
            this.$emit('updateRange', value);
        }
    }
}
</script>

<style>

</style>