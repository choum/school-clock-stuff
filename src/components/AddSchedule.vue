<template>
  <div class="elevation-9">
    <v-toolbar flat color="primary" dark>
      <v-toolbar-title>Add Schedule</v-toolbar-title>
    </v-toolbar>
    <v-container fluid>
      <v-alert type="success" v-show="showAlert"
        >Your schedule has been created.</v-alert
      >
      <v-form ref="form" v-model="valid" lazy-validation>
        <h3>Schedule Name</h3>
        <v-text-field
          v-model="schedName"
          :rules="nameRules"
          color="purple darken-2"
          required
        ></v-text-field>
        <h3>Schedule Description</h3>
        <v-text-field
          v-model="schedDescription"
          :rules="descriptionRules"
          color="purple darken-2"
          required
        ></v-text-field>
        <h3>Schedule Day</h3>
        <v-row>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[0]"
              label="Monday"
              value="Monday"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[1]"
              label="Tuesday"
              value="Tuesday"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[2]"
              label="Wednesday"
              value="Wednesday"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[3]"
              label="Thursday"
              value="Thursday"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[4]"
              label="Friday"
              value="Friday"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[5]"
              label="Saturday"
              value="Saturday"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[6]"
              label="Sunday"
              value="Sunday"
            ></v-checkbox>
          </v-col>
        </v-row>
        <small style="color:red;" v-show="checkValid"
          >Please select one day</small
        >
      </v-form>
      <v-btn dark @click="validate">Create New Schedule</v-btn>
    </v-container>
  </div>
</template>
<script>
export default {
  name: "AddSchedule",
  props: {
    msg: String
  }
};
</script>
<script>
import json from "../assets/data.json";
export default {
  data: () => ({
    valid: false,
    checkValid: false,
    showAlert: false,
    jsonData: json,
    schedule: "",
    selectedDays: [],
    items: [],
    schoolEvents: [],
    schedName: "",
    disabledDays: [],
    schedDescription: "",
    nameRules: [
      v => !!v || "Name is required",
      v => (v && v.length <= 30) || "Name must be less than 30 characters"
    ],
    descriptionRules: [
      v => !!v || "Description is required",
      v =>
        (v && v.length <= 100) || "Description must be less than 100 characters"
    ]
  }),

  created() {
    this.usedDays();
  },
  methods: {
    validate() {
      if (this.$refs.form.validate() && this.selectedDays.length > 0) {
        const newSchedule = {
          Dates: [],
          Description: this.schedDescription,
          DisplayName: this.schedName,
          Events: [],
          Reason: 0,
          WDays: this.selectedDays
        };

        let currentSched = this.jsonData.Schedules;
        currentSched.push(newSchedule);
        this.jsonData.Schedules = currentSched;
        this.showAlert = true;
        this.clearForm();
      } else if (this.$refs.form.validate() && this.selectedDays.length === 0)
        this.checkValid = true;
      else {
        this.checkValid = true;
      }
    },
    usedDays() {
      var usedDays = [];

      // loop through jsonData
      this.jsonData.Schedules.forEach(obj => {
        // get all days that are occupied
        usedDays = usedDays.concat(obj.WDays);
      });

      var currentDisabledDays = [
        false,
        false,
        false,
        false,
        false,
        false,
        false
      ];
      // disable days that are already used
      for (let i = 0; i < usedDays.length; i++) {
        switch (usedDays[i]) {
          case "Monday":
            currentDisabledDays[0] = true;
            break;
          case "Tuesday":
            currentDisabledDays[1] = true;
            break;
          case "Wednesday":
            currentDisabledDays[2] = true;
            break;
          case "Thursday":
            currentDisabledDays[3] = true;
            break;
          case "Friday":
            currentDisabledDays[4] = true;
            break;
          case "Saturday":
            currentDisabledDays[5] = true;
            break;
          case "Sunday":
            currentDisabledDays[6] = true;
            break;
        }
      }
      this.disabledDays = currentDisabledDays;
    },
    clearForm() {
      this.$refs.form.reset();
      this.usedDays();
      this.checkValid = false;
    }
  }
};
</script>
<style scoped>
.v-toolbar .v-input {
  padding-top: 20px !important;
}
</style>
