<template>
  <q-page class="flex column" :class = bgClass>
    <div class="col q-pt-sm q-px-md">
      <q-input 
     
       v-model="search"
        @keyup.enter = "getWeatherBySearch"
        placeholder="Search"
        dark>
        
        

        <template v-slot:before>
          <q-icon 
          @click="getLocation"
          name="my_location" 
          />
        </template>

        

        <template v-slot:append>
          <q-btn
          @click ="getWeatherBySearch"
            round
            dense
            flat
            icon="search" />
        </template>
      </q-input>

    </div>
    
    <template v-if="weatherData">
    <div class="col text-white text-center">

      
          <div class="text-h3 text-weight-medium">
            {{weatherData.name}}
          </div>

          <div class="text-h5 text-weight-medium q-my-sm">
            {{weatherData.weather[0].main}}
          </div>
 
      <div class="text-h1 text-weight-light q-my-lg relative-position q-pl-md">
            <span>{{Math.round(weatherData.main.temp)}}</span>
            <span class ="text-h3 relative-position degree">&deg;C</span>
          </div>
    
          

    </div>

    <div class="col text-center">
      <img :src= "`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" >
    </div>
    </template>

    
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-medium q-mt-xl">
          Upweather
        </div>
      
      <q-btn
        @click="getLocation"
        class = "col q-mb-xl"
        flat>
      <q-icon left size="3em" name="my_location" />
      <div>Find Me</div>
    </q-btn>
            
      </div>


    </template>
   


  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data()
    {
      return {
        search : '',
        weatherData: null,
        lat : null,
        lon: null,
        
        apiUrl :'https://api.openweathermap.org/data/2.5/weather',
        apiKey :'2ba3a4479ff5d00a86c1d22fae30e11c'
       
        
      }
    },
    computed : {
      bgClass(){
      if(this.weatherData)
      {
        if(this.weatherData.weather[0].icon.endsWith('n'))
        {
          return 'bg-night'
        }
        else
        {
          return 'bg-day'
        }
      }
      }
    },
    methods: {

      getLocation()
      {
        this.$q.loading.show()
        navigator.geolocation.getCurrentPosition
        (position => 
        {
          console.log('position = ', position)
          this.lat = position.coords.latitude
          this.lon = position.coords.longitude
          this.getWeatherByCoords()
        })
        
      },
      getWeatherByCoords()
      {
        this.$q.loading.show()
        this.$axios(`${ this.apiUrl }?lat=${ this.lat }&lon=${ this.lon }&appid=${ this.apiKey }&units=metric`).then(response => {
          this.weatherData = response.data
          this.$q.loading.hide()
        })
      },
      getWeatherBySearch()
      {
        //this.$q.loading.show()
        this.$axios(`${ this.apiUrl }?q=${this.search}
       &appid=${ this.apiKey }&units=metric`).then(response => {
          this.weatherData = response.data
         // this.$q.loading.hide()
        })
      },
    }
}
</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to top, #000428, #004e92)
    &.bg-night
      background: linear-gradient(to left, #000000, #434343)
    &.bg-day
      background: linear-gradient(to left, #2193b0, #6dd5ed)

  .degree
    top : -40px

</style>