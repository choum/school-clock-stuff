<template>
  <div class="elevation-9">
    <v-date-picker
      ref="picker"
      v-model="date"
      full-width
      @change="changeDate()"
      :events="functionEvents"
    ></v-date-picker>
    <v-container>
      <v-select
        @change="changeValue()"
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
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday",
      "Sunday"
    ],
    dayColors: ["", "", "", "", "", "", ""]
  }),

  created() {
    this.initialize();
  },
  methods: {
    initialize() {
      this.items = this.jsonData.Schedules;
      this.schoolEvents = [];
      let sched = this.jsonData.Schedules;
      sched.forEach(obj => {
        if (obj.WDays.includes("Sunday")) {
          this.dayColors[6] = obj.Color;
        }
        if (obj.WDays.includes("Monday")) {
          this.dayColors[0] = obj.Color;
        }
        if (obj.WDays.includes("Tuesday")) {
          this.dayColors[1] = obj.Color;
        }
        if (obj.WDays.includes("Wednesday")) {
          this.dayColors[2] = obj.Color;
        }
        if (obj.WDays.includes("Thursday")) {
          this.dayColors[3] = obj.Color;
        }
        if (obj.WDays.includes("Friday")) {
          this.dayColors[4] = obj.Color;
        }
        if (obj.WDays.includes("Saturday")) {
          this.dayColors[5] = obj.Color;
        }
      });
    },
    changeDate() {
      let sched = this.jsonData.Schedules;
      const selectedDate = new Date(Date.parse(this.date));
      const day = selectedDate.getDay();
      //change selected to date
      sched.forEach(obj => {
        if (obj.Dates.includes(this.date)) {
          console.log(1);
          this.schedule = obj.DisplayName;
          sched.length = 0;
        } else if (obj.WDays.includes(this.jsDays[day])) {
          this.schedule = obj.DisplayName;
        }
      });
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
        if (obj.DisplayName === this.schedule.DisplayName) {
          obj.Dates.push(this.date);
        }
      });
      this.jsonData.Schedules = sched;
    },
    functionEvents(date) {
      var day = new Date(date).getDay();
      var curColor = "";
      let sched = this.jsonData.Schedules;
      let bool = false;
      sched.forEach(obj => {
        if (obj.Dates.includes(date)) {
          bool = true;
          curColor = obj.Color;
        }
      });
      if (bool) {
        console.log(1);
        return curColor;
      } else {
        console.log(2);
        return this.dayColors[day];
      }
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
