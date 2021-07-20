<template>
  <section class="section">
    <time-input name="wake-up" :title="$t('wake_up')" v-model="time" />
    <time-input name="current-time" :title="$t('current_time')" v-model="now" :disabled="true"/>
    <time-input name="to-sleep" :title="$t('time_to_fall_asleep')" v-model="toSleep"/>
    <p class="is-size-4 has-text-centered">{{ $t('sleep_cycle_text', { time: timeToSpleep, amount: sleepCycles }) }}</p>
    <clock-chart :currentTime="currentTime" :from="fromMinutes" :to="toMinutes" :toSleep="toSleepMinutes" />

    <b-collapse
      aria-id="contentIdForA11y2"
      class="panel"
      animation="slide">
      <template #trigger>
        <div
          class="panel-heading"
          role="button"
          aria-controls="contentIdForA11y2">
          <strong>{{ $t('sleep_cycle_table') }}</strong>
        </div>
      </template>
      <div class="panel-block">
        <table class="table is-striped is-fullwidth">
          <thead>
            <tr>
              <th>{{ $t('go_to_bed') }}</th>
              <th>{{ $t('sleep_cycles') }}</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="cycle in sleepCycleList" :key="cycle.time">
              <td>{{cycle.time}}</td>
              <td>{{cycle.cycles}}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </b-collapse>
  </section>
</template>

<script>
export default {
  name: 'HomePage',

  data () {
    return {
        sleepCycleLength: 90,
        time: "05:00",
        now: "00:00",
        currentTime: "00:00:00",
        toSleep: "00:30",
        minutesToSleep: null,
        sleepCycles: 0,
    }
  },

  created() {
    setInterval(() => {
      var date = new Date();
      this.now = this.minutesToTime(date.getHours() * 60 + date.getMinutes());
      this.currentTime = this.timeToString(date.getHours(), date.getMinutes(), date.getSeconds());
      this.minutesToSleep = this.getTimeDifference(this.startTime, this.time) % this.sleepCycleLength;
      this.sleepCycles = Math.floor(this.getTimeDifference(this.startTime, this.time) / this.sleepCycleLength);
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
      let time = this.timeToMinutes(this.timeToSpleep) + this.toSleepMinutes > 24 * 60 ? 
        this.timeToMinutes(this.timeToSpleep) + this.toSleepMinutes - 24 * 60 : 
        this.timeToMinutes(this.timeToSpleep) + this.toSleepMinutes;
      return time;
    },

    toMinutes: function () {
      return this.timeToMinutes(this.time);
    },

    toSleepMinutes: function () {
      return this.timeToMinutes(this.toSleep);
    },

    sleepCycleList: function () {
      let cycles = [];
      for (let i = 0; i < this.sleepCycles; i++) {
        let time = this.timeToMinutes(this.timeToSpleep) + this.sleepCycleLength * i <= 24 * 60 ? this.timeToMinutes(this.timeToSpleep) + this.sleepCycleLength * i : this.timeToMinutes(this.timeToSpleep) + this.sleepCycleLength * i - 24 * 60;
        cycles.push({'time': this.minutesToTime(time), 'cycles': this.sleepCycles - i});
      }
      return cycles;
    },
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
      if (minutes == 24 * 60) return '00:00';
      let hours = (Math.floor(minutes / 60)) > 9 ? (Math.floor(minutes / 60)) : '0' + (Math.floor(minutes / 60));
      let min = (minutes % 60) > 9 ? (minutes % 60) : '0' + (minutes % 60);
      let time = hours + ':' + min;
      return time;
    },

    timeToString(hours, minutes, seconds) {
      let hrs = (hours % 60) > 9 ? (hours % 60) : '0' + (hours % 60);
      let min = (minutes % 60) > 9 ? (minutes % 60) : '0' + (minutes % 60);
      let sec = (seconds % 60) > 9 ? (seconds % 60) : '0' + (seconds % 60);

      return hrs + ':' + min + ':' + sec;
    }
  }
}
</script>
