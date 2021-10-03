<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        SUPER SECRET ADMIN STUFFS DONT LOOK OH NO
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <div id="border">
        <v-row no-gutters>
          <v-col cols="10" sm="6" md="6" id="left">
            <AddSchedule />

            <div id="spacer">
              <v-divider></v-divider>
              <Table />
            </div>
          </v-col>
          <v-col cols="8" md="6">
            <Calendar />
          </v-col>
        </v-row>
      </div>
    </v-main>
  </v-app>
</template>

<script>
import AddSchedule from "./components/AddSchedule";
import Table from "./components/Table";
import Calendar from "./components/Calendar";
export default {
  name: "App",

  components: {
    AddSchedule,
    Table,
    Calendar
  },

  data: () => ({
    //
  })
};
</script>
<style>
#border {
  margin: 2%;
}
#left {
  padding-right: 2%;
}
#spacer {
  margin-top: 2%;
}
hr {
  margin-bottom: 2%;
}
.v-toolbar__title {
  font-size: 1.7em;
  font-weight: bold;
}
h2 {
  font-weight: 500 !important;
}
</style>
