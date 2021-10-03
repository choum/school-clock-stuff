<template>
  <div class="elevation-9">
    <div>
      <v-toolbar flat color="primary" dark>
        <v-toolbar-title>Edit Schedule</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-select
          @change="changedValue($event)"
          v-model="schedule"
          :items="items"
          item-text="DisplayName"
          item-value="DisplayName"
          label="Select Schedule"
          persistent-hint
          return-object
          single-line
        ></v-select>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-btn elevation="2">Delete Schedule</v-btn>
      </v-toolbar>
      <v-container fluid v-show="isSelected">
        <h2>Schedule Name</h2>
        <v-text-field
          v-model="schedName"
          color="purple darken-2"
          required
          @change="changeSchedName($event)"
        ></v-text-field>
        <h2>Schedule Day</h2>
        <v-row>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[0]"
              label="Monday"
              value="Monday"
              @change="changeDay($event)"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[1]"
              label="Tuesday"
              value="Tuesday"
              @change="changeDay($event)"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[2]"
              label="Wednesday"
              value="Wednesday"
              @change="changeDay($event)"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[3]"
              @change="changeDay($event)"
              label="Thursday"
              value="Thursday"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[4]"
              @change="changeDay($event)"
              label="Friday"
              value="Friday"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[5]"
              @change="changeDay($event)"
              label="Saturday"
              value="Saturday"
            ></v-checkbox>
          </v-col>
          <v-col cols="2">
            <v-checkbox
              v-model="selectedDays"
              :disabled="disabledDays[6]"
              @change="changeDay($event)"
              label="Sunday"
              value="Sunday"
            ></v-checkbox>
          </v-col>
        </v-row>
      </v-container>
    </div>
    <v-data-table
      v-show="isSelected"
      :headers="headers"
      :items="schoolEvents"
      :items-per-page="-1"
      :custom-sort="sortTime"
      hide-default-footer
    >
      <template v-slot:top>
        <v-toolbar flat>
          <h2>Schedule Day</h2>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                Add Event
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.Description"
                        label="Description"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.Message"
                        label="Message"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.Name"
                        label="Name"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-time-picker
                        v-model="editedItem.Time"
                        label="Time"
                        use-seconds
                      ></v-time-picker>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Cancel
                </v-btn>
                <v-btn color="blue darken-1" text @click="save">
                  Save
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5"
                >Are you sure you want to delete this event?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete"
                  >Cancel</v-btn
                >
                <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                  >OK</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon small class="mr-2" @click="editItem(item)">
          mdi-pencil
        </v-icon>
        <v-icon small @click="deleteItem(item)">
          mdi-delete
        </v-icon>
      </template>
    </v-data-table>
  </div>
</template>

<script>
export default {
  name: "Table",
  props: {
    msg: String
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<script>
import json from "../assets/data.json";
export default {
  data: () => ({
    jsonData: json,
    dialog: false,
    schedule: "",
    dialogDelete: false,
    headers: [
      {
        text: "Description",
        align: "start",
        sortable: false,
        value: "Description",
        sortable: false
      },
      { text: "Message", value: "Message", sortable: false },
      { text: "Name", value: "Name", sortable: false },
      { text: "Time", value: "Time" },
      { text: "Actions", value: "actions", sortable: false }
    ],
    selectedDays: [],
    items: [],
    schoolEvents: [],
    editedIndex: -1,
    editedItem: {
      Description: "",
      Message: "",
      Name: "",
      Time: ""
    },
    schedName: "",
    defaultItem: {
      Description: "",
      Message: "",
      Name: "",
      Time: ""
    },
    isSelected: false,
    disabledDays: []
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Event" : "Edit Event";
    }
  },
  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    }
  },
  created() {
    this.initialize();
  },
  methods: {
    initialize() {
      this.items = this.jsonData.Schedules;
      this.schoolEvents = [];
    },
    editItem(item) {
      this.editedIndex = this.schoolEvents.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    deleteItem(item) {
      this.editedIndex = this.schoolEvents.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },
    deleteItemConfirm() {
      this.schoolEvents.splice(this.editedIndex, 1);
      this.closeDelete();
    },
    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.schoolEvents[this.editedIndex], this.editedItem);
      } else {
        this.schoolEvents.push(this.editedItem);
      }

      // save data into jsonData obj
      let sched = this.jsonData.Schedules;
      if (sched.DisplayName === this.schedule.Displayname) {
        var curEvents = this.schoolEvents;
        curEvents.forEach(obj => {
          if (!Number.isInteger(obj.Time)) {
            let a = obj.Time.split(":");
            let seconds = +a[0] * 60 * 60 + +a[1] * 60 + +a[2];
            obj.Time = seconds;
          }
        });
        sched.Events = curEvents;
      }
      this.jsonData.Schedules = sched;
      this.close();
    },

    changedValue(event) {
      var usedDays = [];

      // loop through jsonData
      this.jsonData.Schedules.forEach(obj => {
        if (event.DisplayName === obj.DisplayName) {
          // get the current events and which days are used for this schedule
          let curEvents = obj.Events;
          curEvents.forEach(obj => {
            if (Number.isInteger(obj.Time)) {
              obj.Time = new Date(obj.Time * 1000).toISOString().substr(11, 8);
            }
          });
          this.schoolEvents = curEvents;
          this.selectedDays = obj.WDays;
          this.schedName = obj.DisplayName;
          this.isSelected = true;
        } else {
          // get all other days that are occupied
          usedDays = usedDays.concat(obj.WDays);
        }
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
    changeDay(event) {
      // grab current sched data and update the Wdays to reflect check boxes
      let sched = this.jsonData.Schedules;
      sched.forEach(obj => {
        if (obj.DisplayName === this.schedule.DisplayName) {
          obj.WDays = event;
        }
      });
      this.jsonData.Schedules = sched;
    },
    changeSchedName(event) {
      let sched = this.jsonData.Schedules;
      sched.forEach(obj => {
        if (obj.DisplayName === this.schedule.DisplayName) {
          obj.DisplayName = this.schedName;
        }
      });
      this.jsonData.Schedules = sched;
    },
    sortTime: function(items) {
      if (items !== undefined) {
        items.forEach(obj => {
          if (Number.isInteger(obj.Time)) {
            obj.Time = new Date(obj.Time * 1000).toISOString().substr(11, 8);
          }
        });
        items.sort((a, b) => a.Time.localeCompare(b.Time));
      }
      console.log(this.jsonData);
      return items;
    }
  }
};
</script>
<style>
.v-toolbar .v-input {
  padding-top: 20px !important;
}
</style>
