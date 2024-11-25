<template>
  <v-app>
    <v-container>
      <v-row justify="center">
        <v-col cols="12" md="8">
          <v-card-title class="headline justify-center">Input Parameters for Sensor Validation</v-card-title>
          <v-card elevation=2 class="rounded-xxl" color="#f2e2ce">
            <v-card-text class="pa-8">
              <v-form ref="form">
                <v-row dense>
                  <v-col cols="12">
                    <v-subheader class="pa-0">Temperature (°C)</v-subheader>
                    <v-text-field
                      v-model="input.temperature"
                      dense
                      outlined
                      required
                      background-color="#fff"
                      rounded
                      placeholder="Enter value"
                      type="number"
                      step="0.01"
                    ></v-text-field>
                    <v-subheader class="pa-0">pH Level</v-subheader>
                    <v-text-field
                      v-model="input.pH"
                      dense
                      outlined
                      required
                      background-color="#fff"
                      rounded
                      placeholder="Enter value"
                      type="number"
                      step="0.01"
                    ></v-text-field>
                    <v-subheader class="pa-0">Current Values</v-subheader>
                    <v-file-input
                      dense
                      outlined
                      required
                      background-color="#fff"
                      rounded
                      placeholder="Upload file"
                      v-model="input.current"
                    ></v-file-input>
                  </v-col>
                </v-row>
                <v-btn
                  color="#d67b09"
                  block
                  class="mt-4 white--text"
                  rounded
                  @click="submitInput"
                  :disabled="!(input.current && input.pH && input.temperature)"
                >
                  Submit for Testing
                </v-btn>
              </v-form>
            </v-card-text>
          </v-card>
        </v-col>

        <v-col cols="12" md="8" class="mt-4">
          <v-card elevation=2 class="rounded-xxl pa-8">
            <v-row no-gutters class="justify-space-between align-center">
              <v-card-title class="title pa-0 font-weight-regular">Calculated Glucose Levels</v-card-title>
              <vue-json-to-csv
                  :json-data="currentTrials"
                  :csv-title="'currant_trial'"
                  :labels="{
                    potential: {title: 'Potential'},
                    current: {title: 'Current'}, 
                    temperature: { title: 'Temperature' },
                    pH: { title: 'pH' },
                    glucose: { title: 'Glucose' }
                  }"
              >
                  <v-btn rounded color="#d67b09" class="white--text" :disabled="!currentTrials.length">
                    <i class="mdi mdi-export-variant mr-1" aria-hidden="true"></i> Export
                  </v-btn>
              </vue-json-to-csv>
            </v-row>
            <v-card-text class="pb-0 px-0 pt-6">
              <v-list dense height="250" class="pa-0">
                <template v-if="currentTrials.length">
                  <v-list-item
                    v-for="(trial, index) in currentTrials"
                    :key="index"
                    class="pa-0"
                  >
                    <v-list-item-content>
                      <v-alert rounded="xxl" color="#f2e2ce" elevation="2" class="ma-0">
                        <p>
                          Trial {{ index + 1 }}:
                        </p>
                        <p>
                          Current: {{ trial.current }}, Potential: {{ trial.potential }}, Temp: {{ trial.temperature }}°C, pH: {{ trial.pH }}
                        </p>
                        <p class="ma-0">
                          Glucose: {{ trial.glucose }} mmol/L
                        </p>
                      </v-alert>
                    </v-list-item-content>
                  </v-list-item>
                </template>
                <template v-else>
                  <div class="d-flex align-center justify-center fill-height">
                    <div class="text-center">
                      <v-icon color="grey lighten-1" size="36">mdi-alert-circle-outline</v-icon>
                      <p>No items available</p>
                    </div>
                  </div>
                </template>
              </v-list>
            </v-card-text>
          </v-card>
        </v-col>

        <v-col cols="12" md="8" class="mt-4">
          <v-card elevation=2 class="rounded-xxl pa-8">
            <v-row no-gutters class="justify-space-between align-center">
              <v-card-title class="title pa-0 font-weight-regular">Previous Trials</v-card-title>
              <vue-json-to-csv
                  :json-data="previousTrials"
                  :csv-title="'all_trials'"
                  :labels="{
                    potential: {title: 'Potential'},
                    current: {title: 'Current'}, 
                    temperature: { title: 'Temperature' },
                    pH: { title: 'pH' },
                    glucose: { title: 'Glucose' }
                  }"
              >
                  <v-btn rounded color="#d67b09" class="white--text" :disabled="!previousTrials.length">
                    <i class="mdi mdi-export-variant mr-1" aria-hidden="true"></i> Export
                  </v-btn>
              </vue-json-to-csv>
            </v-row>
            <v-card-text class="pb-0 px-0 pt-6">
              <v-list dense height="250" class="pa-0">
                <template v-if="previousTrials.length">
                  <v-list-item
                    v-for="(trial, index) in previousTrials"
                    :key="index"
                    class="pa-0"
                  >
                    <v-list-item-content>
                      <v-alert rounded="xxl" color="#f2e2ce" elevation="2" class="ma-0">
                        <p>
                          Trial {{ index + 1 }}:
                        </p>
                        <p>
                          Current: {{ trial.current }}, Potential: {{ trial.potential }}, Temp: {{ trial.temperature }}°C, pH: {{ trial.pH }}
                        </p>
                        <p class="ma-0">
                          Glucose: {{ trial.glucose }} mmol/L
                        </p>
                      </v-alert>
                    </v-list-item-content>
                  </v-list-item>
                </template>
                <template v-else>
                  <div class="d-flex align-center justify-center fill-height">
                    <div class="text-center">
                      <v-icon color="grey lighten-1" size="36">mdi-alert-circle-outline</v-icon>
                      <p>No items available</p>
                    </div>
                  </div>
                </template>
              </v-list>
            </v-card-text>
          </v-card>
        </v-col>


      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import vueJsonToCsv from 'vue-json-to-csv';
import axios from 'axios'

var BASE_URL = "http://127.0.0.1:8000/"

export default {
  name: "Home",
  data() {
    return {
      input: {
        temperature: "",
        current: null,
        pH: ""
      },
      currentTrials: [],
      previousTrials: []
    };
  },
  mounted() {
    if (sessionStorage.getItem("previousTrials")) {
      this.previousTrials = JSON.parse(sessionStorage.getItem("previousTrials"));
    }
  },
  methods: {
    async submitInput() {
      console.log(this.input)
      var fd = new FormData()
      fd.append('temperature', this.input.temperature)
      fd.append('ph', this.input.pH)
      fd.append('ps_data', this.input.current)
      await axios.post(BASE_URL + "predict/", fd)
      .then(res => {
        if (res.status === 200) {
          this.$toast.success('Prediction Successful!', {position: 'top-right', duration: 4000});
          res.data.prediction.forEach((item, index) => {
            this.$set(this.currentTrials, index, {temperature: res.data.temperature, pH: res.data.ph, potential: res.data.potential[index].toFixed(2), current: res.data.current[index], glucose: item.toFixed(2)})
            this.$set(this.previousTrials, this.previousTrials.length, {temperature: res.data.temperature, pH: res.data.ph, potential: res.data.potential[index].toFixed(2), current: res.data.current[index], glucose: item.toFixed(2)})
          })
          sessionStorage.setItem("previousTrials", JSON.stringify(this.previousTrials));
        } else {
          this.$toast.error(res.response.data.detail, {position: 'top-right', duration: 4000});
        }
      }).catch(err => {
        this.$toast.error(err.response.data.detail, {position: 'top-right', duration: 4000});
      })
    }
  },
  components: {
    vueJsonToCsv
  }
};
</script>

<style>
.v-list-item-title {
  font-size: 16px;
}
.v-list{
  overflow-y:auto
}
</style>