<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input @keyup.enter="getWeatherBySearch" v-model="search" placeholder="Search" dark borderless>
        <template v-slot:before>
          <q-icon
            @click="getLocation"
            name="my_location"
          />
        </template>
        <template v-slot:append>
          <q-btn @click="getWeatherBySearch" round dense flat icon="search" />
        </template>
      </q-input>

    </div>
    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{ weatherData.name}}
        </div>
        <div class="text-h6 text-weight-light">
          {{ weatherData.weather[0].main }}
        </div> 
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{Math.round(weatherData.main.temp-273.15)}}</span>
          <span class="text-h4 relative-position degree">&deg;C</span>
        </div>
      </div>
      <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" alt="">
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weigth-thin">
          Weather
        </div>
        <q-btn
          @click="getLocation"
          class="col" flat>
          <q-icon left size="3em" name="my_location" />
          <div>Find My Location</div>
        </q-btn>
      </div>

    </template>
      <div class="col skyline">
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue';

export default{
  name: 'PageIndex',
  data(){
    return{
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      apiKey: '01aef5c6622ae523bec1d469e2bbfdcc'
    }
  },
  computed: {
    bgClass(){
      if (this.weatherData){
        if (this.weatherData.weather[0].icon.endsWith('n')){
          return 'bg-night'
        }
        else{
          return 'bg-day'
        }
      }
    }
  },
  methods:{
    getLocation(){
       this.$q.loading.show() 
        navigator.geolocation.getCurrentPosition(position => {
        this.lat = position.coords.latitude
        this.lon = position.coords.longitude
        this.getWeatherByCoords()
      })
    },
    getWeatherByCoords(){
      this.$q.loading.show()
      this.$axios(`${ this.apiUrl }?lat=${ this.lat }&lon=${ this.lon }&appid=${ this.apiKey }&units=matric`).then(response =>{
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    },
    getWeatherBySearch(){
      this.$q.loading.show()
      this.$axios(`${ this.apiUrl }?q=${this.search}&appid=${ this.apiKey }&units=matric`).then(response =>{
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    }   
  }
}
</script>

<style lang="sass">

  .q-page
    background: linear-gradient(to top, #e6dada, #274046)
    &.bg-night
      background: linear-gradient(to bottom, #0f2027, #728e9c)
  .degree
    top:-44px
  .skyline
    flex: 0 0 100px
    background: url(../statics/skyline.png)
    background-size: contain
    background-position: center bottom

</style>
