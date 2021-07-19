<template>
    <div class="wrapper">
        <chart class="chart" :chartType="type" :chartData="chartData" :chartOptions="chartOptions" />
    </div>
</template>

<script>
import ClockChart from './ClockChart.vue';
export default {
  components: { ClockChart },
    props: {
        from: {
            type: Number,
            default: 0
        },
        to: {
            type: Number,
            default: 0
        },
        toSleep: {
            type: Number,
            default: 0
        }
    },

    data () {
        return {
            type: 'pie',
            options: {
                responsive: true,
                plugins: {
                    legend: false,
                }  
            },
        }
    },

    computed: {
        chartData: function() {
            let sleepDuration = this.to - this.from;
            if(this.from > this.to) sleepDuration = (24 * 60) - this.from + this.to;

            return {
                labels: [
                    'Falling Asleep',
                    'Sleep',
                    'Awake'
                ],
                datasets: [{
                    label: 'Sleepcalc',
                    data: [this.toSleep, sleepDuration, (24 * 60) - sleepDuration - this.toSleep],
                    backgroundColor: [
                        '#aced5c',
                        '#ffd863'
                    ],
                    hoverOffset: 4
                }]
            }
        },

        chartOptions: function () {
            return {
                responsive: true,
                plugins: {
                    legend: false,
                },
                rotation: this.rotation
            }
        },

        rotation: function () {
            let totalTime = 24 * 60;
            let sleepStart = this.from - this.toSleep;
            let rotationPercentage = 1 / totalTime * sleepStart;
            return rotationPercentage * (2 * Math.PI) - 0.5 * Math.PI;
        }
    }
}
</script>

<style scoped>
    .wrapper {
        margin: 20px 0;
    }
</style>