<template>
    <div>
        <canvas ref="canvas"></canvas>
    </div>
</template>

<script>
import Chart from 'chart.js';

export default {
    props: {
        chartType: {
            type: String,
            required: true
        },
        chartData: {
            required: true
        },
        chartOptions: {
            required: true
        }
    },

    data () {
        return {
            chart: null,
        }
    },

    mounted() {
        this.chartConstructor(this.chartType, this.chartData, this.chartOptions);
    },

    methods: {
        chartConstructor: function (chartType, chartData, chartOptions) {
            const chartElement = this.$refs['canvas'];
            this.chart = new Chart(chartElement, {
                type: chartType,
                data: chartData,
                options: chartOptions,
            });
        },
    },

    watch: {
        chartData: {
            handler: function (val) {
                this.chart.data = this.chartData;
                this.chart.update();
            },
            deep: true
        }
    }
}
</script>