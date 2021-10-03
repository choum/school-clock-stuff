<template>
  <div class="elevation-9">
    <v-date-picker
      ref="picker"
      v-model="date"
      full-width
      @change="changeDate()"
    ></v-date-picker>
    <v-container>
      <v-select
        @change="changedValue()"
        v-model="schedule"
        :items="items"
        item-text="DisplayName"
        item-value="DisplayName"
        label="Select Schedule"
        persistent-hint
        return-object
        single-line
      ></v-select>
    </v-container>
  </div>
</template>

<script>
import json from "../assets/data.json";
export default {
  name: "Calendar",

  data: () => ({
    date: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
      .toISOString()
      .substr(0, 10),
    items: [],
    jsonData: json,
    schedule: "",
    jsDays: [
      "Sunday",
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday",
      "Sunday"
    ]
  }),

  created() {
    this.initialize();
  },
  methods: {
    initialize() {
      this.items = this.jsonData.Schedules;
      this.schoolEvents = [];
    },
    changeDate() {
      let sched = this.jsonData.Schedules;
      const selectedDate = new Date(Date.parse(this.date));
      const day = selectedDate.getDay();

      //change selected to date
      sched.forEach(obj => {
        if (obj.WDays.includes(this.jsDays[day])) {
          this.schedule = obj.DisplayName;
        }
      });
      console.log(this.date);
    },
    changeValue() {
      let sched = this.jsonData.Schedules;
      sched.forEach(obj => {
        //check if date exists in a schedule already
        if (obj.Dates.includes(this.date)) {
          let newDates = obj.Dates;
          newDates.pop(this.date);
          obj.Dates = newDates;
        }
        //add date to schedule
        if (obj.DisplayName === this.schedule) {
          obj.Dates.push(this.date);
        }
      });
      this.jsonDate.Schedules = sched;
    }
  }
};
</script>

<style>
.v-date-picker-table,
.v-date-picker-table table {
  height: 500px !important;
}

.v-date-picker-table .v-btn__content {
  font-size: 20px !important;
}
.accent--text {
  font-size: 25px;
}
.v-date-picker-table th {
  font-size: 20px;
}
</style>
