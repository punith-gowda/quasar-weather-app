<template>
  <q-page class="flex column" :class="bgclass">
    <div class="col q-pt-lg q-px-md">
      <q-input v-model="search" placeholder="search" dark borderless
      @keyup.enter="getweatherBysearch">
        <template v-slot:before>
          <q-icon @click="getLocation" name="my_location" />
        </template>
        <template v-slot:append >
          <q-btn @click="getweatherBysearch" dense flat icon="search" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">{{weatherData.name}}</div>

        <div class="text-h6 text-weight-light">{{weatherData.weather[0].main}}</div>

        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp)}}</span>
          <span class="text-h4 relative-position degree">&deg;C</span>
        </div>
      </div>

      <div class="col text-center">
        <img :src= "`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Chill
          <br />Weather
        </div>
        <q-btn @click="getLocation" class="col" flat>
          <q-icon left size="3em" name="my_location" />
          <div>Find my location</div>
        </q-btn>
      </div>
    </template>
    <div class="col skyline"></div>
  </q-page>
</template>

<script>
export default {
  data() {
    return {
      search: "",
      weatherData: null,
      lat: null,
      lon: null,
      apiURL: "https://api.openweathermap.org/data/2.5/weather",
      apiKey:'780fc6d3b59029513da920f88a7250ea'
    };
  },
  methods: {
    getLocation() {
      this.$q.loading.show()
      navigator.geolocation.getCurrentPosition((position) => {
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
        this.getWeatherBycoords();
         this.$q.loading.hide()
      });
    },
    getWeatherBycoords() {
       this.$q.loading.show()
      this.$axios(
        `${ this.apiURL }?lat=${ this.lat }&lon=${ this.lon }&appid=${ this.apiKey }&units=metric`).then(response=> {
         this.weatherData=response.data
          this.$q.loading.hide()
        });
    },
    getweatherBysearch() {
       this.$q.loading.show()
      this.$axios(
        `${ this.apiURL }?q=${this.search}&appid=${ this.apiKey }&units=metric`).then(response=> {
         this.weatherData=response.data
          this.$q.loading.hide()
        });
    },
  },
  computed:
  {
    bgclass(){
      if(this.weatherData){
        if(this.weatherData.weather[0].icon.endsWith('n')){
          return 'bg-night'
        }
        else{
          return 'bg-day'
        }
      }
      

    }
  }
};
</script>

<style lang="scss">
	.q-page{
		background: linear-gradient(to bottom, #136a8a, #267871);
		&.bg-night{
			background: linear-gradient(to bottom, #232526, #414345);
		}
		&.bg-day{
			background: linear-gradient(to bottom, #00b4db, #0083b0);
		}
	}
	.degree{
		top: -44px;
	}
	
	.skyline{
		flex: 0 0 100px;
		background: url(../assets/skyline.svg);
		background-size: contain;
		background-position: center bottom;
	}
		
	
	
</style>
