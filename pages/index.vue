<template>
  <section class="section">
    <time-input name="wake-up" title="Wake up" v-model="time" />
    <time-input name="current-time" title="Current time" v-model="now"/>
    <time-input name="to-sleep" title="Time to fall asleep" v-model="toSleep"/>
    <p class="is-size-4 has-text-centered">You need to go to bed at {{ timeToSpleep }} to have {{ sleepCycles }} sleep cycles.</p>
    <sleep-chart :toSleep="toSleepMinutes" :from="fromMinutes" :to="toMinutes" />
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
        toSleep: "00:30",
        minutesToSleep: null,
        sleepCycles: 0,
    }
  },

  created() {
    setInterval(() => {
      var date = new Date();
      this.now = this.minutesToTime(date.getHours() * 60 + date.getMinutes());
      this.minutesToSleep = this.getTimeDifference(this.startTime, this.time) % 90;
      this.sleepCycles = Math.floor(this.getTimeDifference(this.startTime, this.time) / 90);
    }, 1000)
  },
  
  computed: {
    startTime: function () {
      let minutes = this.timeToMinutes(this.now) + this.toSleepMinutes;
      return this.minutesToTime(minutes);
    },

    timeToSpleep: function () {
      let minutes = this.timeToMinutes(this.now) + this.minutesToSleep;
      return this.minutesToTime(minutes);
    },

    fromMinutes: function () {
      return this.timeToMinutes(this.timeToSpleep) + this.toSleepMinutes;
    },

    toMinutes: function () {
      return this.timeToMinutes(this.time);
    },

    toSleepMinutes: function () {
      return this.timeToMinutes(this.toSleep);
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
      let timeArray = time.split(':');
      let hours = parseInt(timeArray[0]);
      let minutes = parseInt(timeArray[1]);
      return hours * 60 + minutes;
    },

    minutesToTime(minutes) {
      let hours = (Math.floor(minutes / 60)) > 9 ? (Math.floor(minutes / 60)) : '0' + (Math.floor(minutes / 60));
      let min = (minutes % 60) > 9 ? (minutes % 60) : '0' + (minutes % 60);
      let time = hours + ':' + min;
      return time;
    }
  }
}
</script>
