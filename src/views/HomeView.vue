<template>
  <v-overlay v-model="overlay">
    <v-container class="align-center-justify-center">
      <v-progress-circular indeterminate size="128">
        Waiting...
      </v-progress-circular>
    </v-container>
  </v-overlay>
  <v-app >
    <v-app-bar app color="blue-lighten-1" height="60" rounded>

      <v-toolbar-title class="white--text text-center" text-center>Hier gibt es das beste Wetter
        <v-icon right color="yellow " size="large">mdi-white-balance-sunny</v-icon>
      </v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-row justify="space-between" >
          <v-col cols="12" md="2" lg="2" xl="2">
            <v-btn color="success" @click="updateWeather">Select</v-btn>
          </v-col>
          <v-col cols="12" md="10" lg="10" xl="10">
            <v-text-field label="Region" v-model="region" clearable></v-text-field>
          </v-col>

        </v-row>
        <v-row>
          <v-col cols="6" md="6" lg="6" xl="6">
            <v-slider
                v-model="value" label="Einwohner" :min = "50000" :max = "1000000" :step="50000" thumb-label></v-slider>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="6" md="6" lg="6" xl="6">
            <v-slider
                v-model="radius" label="Radius" :min = "50" :max = "300" :step="50" thumb-label></v-slider>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" sm="6" md="6" lg="6" xl="6" v-for="(forecast, index) in weatherForecasts" :key="index">
            <v-card class="mb-3">
              <v-card-title>{{ forecast.weather.city }} </v-card-title>
              <v-card-text>
                <p>
                 <span class="temperature-text"> <v-icon color="red" size="small">mdi-heart</v-icon> {{forecast.weather.feels_like}}°C</span>
                </p>
                <p>
                  <span class="large-text">{{ forecast.weather.max }}°C / </span>
                  <span class="small-text">{{ forecast.weather.min }}°C.</span>
                </p>

              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>


<script>
import {ref} from 'vue'
const value = ref(0)
const overlay = ref(false)
export default {
  created() {
    this.updateWeather()
  },
  data() {
    return {
      overlay: false,
      drawer: false,
      region: 'Regensburg',
      radius: 50,
      value: 50000,

      weatherForecasts: [
      ]
    };
  },
  methods: {
    updateWeather() {
      this.overlay = true
      this.weatherForecasts = {}
      this.$http.get('https://us-central1-wetterapp-9b933.cloudfunctions.net/app/weather', {
        params: {
          region: this.region,
          radius: this.radius,
          population: this.value,

        }
      })
          .then(response => {
            this.weatherForecasts = response.data
            this.overlay = false;
          })
          .catch(error => {
            console.error('Fehler beim GET-Request:', error);
          });
    }
  },

};

</script>
<style>
.align-center-justify-center {
  display: flex;
  justify-content: center; /* Horizontal zentrieren */
  align-items: center; /* Vertikal zentrieren */
  width: 100%;
  height: 100%;
}
.temperature-text {
  font-size: 20px;
}
.large-text {
  font-size: 15px;
}

.small-text {
  font-size: 13px;
}

</style>