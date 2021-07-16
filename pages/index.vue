<template>
  <section class="section">
    <time-input name="wake-up" title="Wake up" v-model="time" />
    <time-input name="current-time" title="Current time" v-model="now"/>
    <sleep-chart :from="fromMinutes" :to="toMinutes" />
    <p>You need to go to bed at {{ timeToSpleep }} to have {{ sleepCycles }} sleep cycles.</p>
  </section>
</template>

<script>
import SleepChart from '~/components/SleepChart.vue';
export default {
  components: { SleepChart },
  name: 'HomePage',

  data () {
    return {
        time: "05:00",
        now: "00:00",
        minutesToSleep: null,
        sleepCycles: 0,
    }
  },

  created() {
    setInterval(() => {
      var date = new Date();
      var hours = date.getHours().toString().length == 2 ? date.getHours() : "0" + date.getHours();
      var minutes = date.getMinutes().toString().length == 2 ? date.getMinutes() : "0" + date.getMinutes();
      this.now = hours + ":" + minutes;
      this.minutesToSleep = this.getTimeDifference(this.now, this.time) % 90;
      this.sleepCycles = Math.floor(this.getTimeDifference(this.now, this.time) / 90);
    }, 1000)
  },
  
  computed: {
    timeToSpleep: function () {
      let minutes = this.timeToMinutes(this.now) + this.minutesToSleep;

      return (Math.floor(minutes / 60)) + ':' + (minutes % 60);
    },

    fromMinutes: function () {
      return this.timeToMinutes(this.timeToSpleep);
    },

    toMinutes: function () {
      return this.timeToMinutes(this.time);
    }
  },

  methods: {
    getTimeDifference(time1, time2) {
      if (time1 == time2) return 24 * 60;

      let total1 = this.timeToMinutes(time1);
      let total2 = this.timeToMinutes(time2);

      if (total1 > total2) return (24 * 60) - total1 + total2;
      return total2 - total1;
    },

    timeToMinutes(time) {
      let hours = parseInt(time.split(':')[0]);
      let minutes = parseInt(time.split(':')[1]);
      return hours * 60 + minutes;
    }
  }
}
</script>
