<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input 
        v-model="search" 
        placeholder="Search"
        dark
        borderless
        @keyup.enter="getWeatherBySearch"
      >
        <template v-slot:before>
          <q-icon
            name="my_location"
            @click="getLocation" />
        </template>
  
        <template v-slot:append>
          <q-btn
            @click="getWeatherBySearch"
            round
            dense
            flat
            icon="search" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{ weatherData.name }}
        </div>
        <div class="text-h6 text-weight-light">
          {{ weatherData.weather[0].main }}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp) }}</span>
          <span class="text-h4 relative-position degree">&deg;C</span>
        </div>
      </div>

      <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
      </div>
    </template>
    
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar<br>Weather
        </div>
        <q-btn
          class="col"
          flat
          @click="getLocation"
        >
          <q-icon left size="3em" name="my_location"/>
          <div>Find my location</div>
        </q-btn>
      </div>
    </template>

    <div style="background: url(statics/skyline.png);" class="skyline"></div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiURL: 'https://api.openweathermap.org/data/2.5/weather'
    }
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith('n')) {
          return 'bg-night'
        } else {
          return 'bg-day'
        }
      }
    }
  },
  methods: {
    getLocation() {
      this.$q.loading.show()
      navigator.geolocation.getCurrentPosition(position => {
        console.log('position: ', position);
        this.lat = position.coords.latitude
        this.lon = position.coords.longitude
        this.getWeatherByCoords()
        this.$q.loading.hide()
      })
    },
    getWeatherByCoords() {
      // f8e8c841fec6b9b0cc05b471667d1406
      this.$q.loading.show()
      this.$axios(`${this.apiURL}?lat=${this.lat}&lon=${this.lon}&appid=9ddde0b03676257514a53ed42d2cf4f6
&units=metric`)
        .then(response => {
          this.weatherData = response.data
          this.$q.loading.hide()
        })
    },
    getWeatherBySearch() {
      this.$q.loading.show()
      this.$axios(`${this.apiURL}?q=${this.search}&appid=9ddde0b03676257514a53ed42d2cf4f6
&units=metric`)
        .then(response => {
          this.weatherData = response.data
          this.$q.loading.hide()
        })
    }
  }
}
</script>

<style lang="scss">
  .q-page {
    background: linear-gradient(to bottom, #396afc, #2948ff);
    &.bg-night {
      background: linear-gradient(to bottom, #0f2027, #203a43, #2c5364);
    }
    &.bg-day {
      background: linear-gradient(to bottom, #00b4db, #0083b0);
    }
  }

  .degree {
    top: -44px
  }

  .skyline {
  flex: 0 0 100px;
  background-size: contain;
  background-position: center bottom;
}
</style>
