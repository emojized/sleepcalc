<template>
    <div>
        <canvas ref="chart"></canvas>
    </div>
</template>

<script>
class Timeframe {
    constructor(near, far, angleFrom, angleTo, text) {
        this.nead = near;
        this.far = far;
        this.angleFrom = angleFrom;
        this.angleTo = angleTo;
        this.text = text;
    }

    checkCollision(x, y) {
        let angle = Math.atan(x, y);
        if (angle < this.angleFrom || angle > tis.angleTo) return false;

        let distance = Math.sqrt(x * x + y * y);
        if (distance < this.near || distance > this.far) return false;

        return true;
    }
}

export default {
    props: {
        currentTime: {
            type: String,
            required: true
        },

        from: {
            type: Number
        },

        to: {
            type: Number
        },

        toSleep: {
            type: Number
        }
    },

    data () {
        return {
            clockRadius: 0.8,
            timeframes: [],
        }
    },

    mounted() {
        const chartElement = this.$refs['chart'];
        const ctx = chartElement.getContext('2d');
        this.ctx = ctx;
        this.ctx.translate(chartElement.clientWidth / 2, chartElement.clientHeight / 2);

        let size = chartElement.clientHeight / 2;
        this.size = size;

        this.setup();

        window.addEventListener('resize', this.setup);
    },

    methods: {
        setup: function () {
            const chartElement = this.$refs['chart'];
            const ctx = chartElement.getContext('2d');
            // calculate the smaller side
            let size = chartElement.clientHeight > chartElement.clientWidth ? chartElement.clientWidth : chartElement.clientHeight;
            this.size = size / 2;

            ctx.canvas.width  = chartElement.clientWidth;
            ctx.canvas.height = chartElement.clientHeight;
            this.ctx.translate(chartElement.clientWidth / 2, chartElement.clientHeight / 2);
            
            this.generate();  
        },

        generate: function () {
            let toSleepFrom = (this.from - this.toSleep) >= 0 ? (this.from - this.toSleep) : 24 * 60 + (this.from - this.toSleep);
            let toSleepTo = this.from;
            
            this.clearCanvas();

            // draw pm time
            this.drawOuterTimeframe(toSleepFrom, toSleepTo, '#aced5c');
            this.drawOuterTimeframe(this.from, this.to, '#ffd863');
            this.drawArc(0, 0, this.size * 0.91, 0, 2 * Math.PI, '#fff');

            // draw am time
            this.drawInnerTimeframe(toSleepFrom, toSleepTo, '#aced5c');
            this.drawInnerTimeframe(this.from, this.to, '#ffd863');
            this.drawArc(0, 0, this.size * 0.82, 0, 2 * Math.PI, '#fff');

            // add the clock
            this.drawClock(this.size * this.clockRadius, this.currentTime);
        },

        clearCanvas() {
            this.ctx.save();
            this.ctx.setTransform(1, 0, 0, 1, 0, 0);

            this.ctx.clearRect(0, 0, this.$refs['chart'].width, this.$refs['chart'].height);

            this.ctx.restore();
        },

        drawClock: function (radius, time) {
            // draw outer rim
            this.drawArc(0, 0, radius, 0 * Math.PI, 2 * Math.PI, '#000');
            this.drawArc(0, 0, radius * 0.95, 0 * Math.PI, 2 * Math.PI, '#fff');

            // draw minute lines
            for (let i = 0; i < 60; i++) {
                this.drawCircleLine(0, 0, (2 * Math.PI / 60) * i, radius * 0.15, radius * 0.75, '#000', 1);
            }

            // draw hour lines
            for (let i = 0; i < 12; i++) {
                this.drawCircleLine(0, 0, (2 * Math.PI / 12) * i, radius * 0.25, radius * 0.65, '#000', 3);
            }

            let timeSegments = time.split(':');

            // draw hour
            this.drawCircleLine(0, 0, (2 * Math.PI / 12) * timeSegments[0], radius * 0.60, 0, '#000', 5);

            // draw minute
            this.drawCircleLine(0, 0, (2 * Math.PI / 60) * timeSegments[1], radius * 0.80, 0, '#000', 5);

            // draw second
            this.drawCircleLine(0, 0, (2 * Math.PI / 60) * timeSegments[2], radius * 0.80, 0, '#ff0000', 1);

            // draw middle circle
            this.drawArc(0, 0, radius * 0.02, 0 * Math.PI, 2 * Math.PI, '#000');
        },

        drawArc: function (x, y, radius, start, end, color) {
            let ctx = this.ctx;
            ctx.beginPath();
            ctx.moveTo(x, y);
            ctx.arc(x, y, radius, start, end);
            ctx.closePath();
            ctx.fillStyle = color;
            ctx.fill();
        },

        drawTimeframe: function (x, y, radius, fromTime, toTime, color) {
            let fromAngle = (2 * Math.PI) / (12 * 60) * fromTime - 0.5 * Math.PI;
            let toAngle = (2 * Math.PI) / (12 * 60) * toTime - 0.5 * Math.PI;
            this.drawArc(x, y, radius, fromAngle, toAngle, color);
            this.timeframes.push(new Timeframe(radius - 8, radius, fromAngle, toAngle, "Test"));
        },

        drawOuterTimeframe: function (fromTime, toTime, color) {
            if (fromTime <= (12 * 60) && toTime <= (12 * 60) && fromTime < toTime) return;
            let from = fromTime <= (12 * 60) ? 12 * 60 : fromTime - 12 * 60;
            let to = toTime <= (12 * 60) ? 0 : toTime - 12 * 60;
            this.drawTimeframe(0, 0, this.size * .99, from, to, color);
        },

        drawInnerTimeframe: function (fromTime, toTime, color) {
            if (fromTime > (12 * 60) && toTime > (12 * 60) && fromTime < toTime) return;
            let from = fromTime > 12 * 60 ? 0 : fromTime;
            let to = toTime > 12 * 60 ? 12 * 60 : toTime;
            this.drawTimeframe(0, 0, this.size * .90, from, to, color);
        },

        drawCircleLine: function (x, y, angle, length, distance, color, width) {
            let correctedAngle = angle - 0.5 * Math.PI;
            let startX = x + Math.cos(correctedAngle) * distance;
            let startY = y + Math.sin(correctedAngle) * distance;
            let endX = x + Math.cos(correctedAngle) * (distance + length);
            let endY = y + Math.sin(correctedAngle) * (distance + length);

            let ctx = this.ctx;
            ctx.beginPath();
            ctx.moveTo(startX, startY);
            ctx.lineTo(endX, endY);
            ctx.closePath();
            ctx.strokeStyle  = color;
            ctx.lineWidth = width;
            ctx.stroke();
        },
    },

    beforeDestroy: function () {
         window.removeEventListener('resize', this.setup);
    },

    watch: {
        currentTime: {
            handler: function (val) {
                this.drawClock(this.size * this.clockRadius, this.currentTime);
            },
            deep: true
        },

        from: {
            handler: function (val) {
                this.generate();
            },
            deep: true
        },

        to: {
            handler: function (val) {
                this.generate();
            },
            deep: true
        },

        toSleep: {
            handler: function (val) {
                this.generate();
            },
            deep: true
        }
    }
}
</script>

<style scoped>
    canvas {
        height: 500px;
        width: 100%;
    }
</style>