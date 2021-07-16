<template>
    <div class="wrapper">
        <chart class="chart" :chartType="type" :chartData="chartData" :chartOptions="options" />
    </div>
</template>

<script>
export default {
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
        }
    }
}
</script>

<style scoped>
    .wrapper {
        margin: 20px 0;
    }
</style>