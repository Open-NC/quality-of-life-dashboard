<template lang="html">
    <div id="years" v-if="sharedState.metric.years.length > 1" >
        <div class="flex-container">
            <div class="flex-left">
                <div class="playpause">
                   <input type="checkbox" value="None" id="playpause" name="check" v-on:change="play" checked title="Start or stop playing through data timeseries by year"/>
                   <label for="playpause"></label>
                 </div>
            </div>
            <div v-if="sharedState.metric.years.length > 1" class="flex-center yearslider">
                <input id="yearslider" type="range" v-bind:min="sharedState.metric.years[0]"
                    v-bind:value="sharedState.year" v-bind:max="sharedState.metric.years[sharedState.metric.years.length - 1]"
                    v-on:change="changeYear" step="1" list="ticks">
                <datalist id="ticks">
                    <option v-for="year in sharedState.metric.years">
                        {{ year }}
                    </option>
                </datalist>
            </div>
            <div class="flex-right">
                <h3>{{ sharedState.year }}</h3>
            </div>
        </div>
        <div class="flex-container">
            <label for="yearslider">use slider to choose a data year to view</label>
        </div>
    </div>
</template>

<script>
export default {
    name: 'sc-year',
    methods: {
        changeYear: function(event) {
            let closest = this.getClosest(this.sharedState.metric.years, event.target.value);
            this.sharedState.year = event.target.value = closest;
        },
        getClosest: function(arr, val) {
            return arr.reduce(function (prev, curr) {
                return (Math.abs(curr - val) < Math.abs(prev - val) ? curr : prev);
            });
        },
        play: function($event) {
            let _this = this;
            const checked = $event.target.checked;

            if (!checked) {
              // set current index and advance one
              let i = _this.sharedState.metric.years.indexOf(_this.sharedState.year) + 1;
              i >= _this.sharedState.metric.years.length ? i = 0 : null;
              _this.sharedState.year = _this.sharedState.metric.years[i];

              _this.privateState.playToggle = setInterval(function() {
                // begin the loop
                i++;
                if (i >= _this.sharedState.metric.years.length) {
                  i = 0;
                }
                _this.sharedState.year = _this.sharedState.metric.years[i];
              }, 1500);
            } else {
              // end loop
              clearInterval(_this.privateState.playToggle);
            }

        }
    }
}
</script>

<style lang="css" scoped>
#years {
    color: #262626;
    padding: 5px;
    display: inline-block;
    width: 100%;
}
.flex-container {
    display: flex;
    align-items: center;
}
.flex-left, .flex-right {
    width: 20px;
}
.flex-center {
    flex-grow: 1;
    padding: 0 10px;
}
#yearslider {
    width: 100%;
}
h3 {
    margin: 0;
    font-size: 1.1em;
}

.playpause label {
    display: block;
    box-sizing: border-box;
    width: 0;
    height: 34px;
    border-color: transparent transparent transparent rgb(158, 158, 158);
    transition: 180ms all ease;
    cursor: pointer;
    border-style: double;
    border-width: 0 0 0 25px;
}
.playpause label:hover {
    border-color: transparent transparent transparent #00688B;
}
.playpause input[type="checkbox"] {
    display: none;
}
.playpause input[type="checkbox"]:checked + label {
    border-style: solid;
    border-width: 17px 0 17px 25px;
}

#years label {
    width: 100%;
    text-align: center;
    color: rgb(158, 158, 158);
    font-weight: normal;
}
</style>
