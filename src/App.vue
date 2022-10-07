<template>
<div id="app" :class="typeof weather_info.main != 'undefined' ? weather_info.weather[0].main : ''">
<main>
  <div class="search-box">
    <input type="text" v-model="location" @keypress="fetchWeather" class="search-bar" placeholder="Type a location..."/>
  </div>
  <div class="weather-wrap" v-if="typeof weather_info.main != 'undefined'">
    <div class="location-box">
      <div class="location">Current Weather</div>
    </div>
    <div class="weather-box">
      <div class="temp">
        {{getFormatedTemp()}}
        <img :src="url_imgbase + weather_info.weather[0].icon + '@2x.png'"/>
        {{weather_info.weather[0].main}}
      </div>
    </div>
    <div class="location-box">
      <div class="location">{{ weather_info.name }}, {{ weather_info.sys.country }}</div>
      <div class="date">{{getFormatedDate()}}</div>
    </div>
    <div class="weather-box">      
      <div class="weather">
        <div>Humidity</div>
        <div>{{weather_info.main.humidity}} %</div>    
        <div>Wind</div>
        <div>{{weather_info.wind.speed}} m/s</div>
        <div>Sunrise</div>
        <div>{{getFormatedTime(weather_info.sys.sunrise)}}</div>
        <div>Sunset</div>
        <div>{{getFormatedTime(weather_info.sys.sunset)}}</div>        
      </div>
    </div>
    <div class="set-f-option">
      °C <custom-button @changeValue="showF = !showF"></custom-button> °F
    </div>
  </div>
</main>
</div>
</template>

<script>
import CustomButton from './components/ToggleButton.vue'

export default {
  name: 'app',
  components: { CustomButton },
  data () {
    return {      
      url_base: 'https://api.openweathermap.org/data/2.5/', //openweathermap api url
      api_key: '979aea19483ddbb8ba65e61bdd796d5b', //openweathermap api key
      url_imgbase: 'https://openweathermap.org/img/wn/', //openweathermap icon location      
      geo_url:'https://ipgeolocation.abstractapi.com/v1/',
      geo_api_key:'1be9a6884abd4c3ea143b59ca317c6b2',
      location: '',
      showF: false,
      weather_info: {}
    }
  },
  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        this.fetchWeatherApi(this.location)
      }
    },
    fetchWeatherApi(query) {
      if (localStorage.getItem(query) != null) {//Check if query result is already stored locally
        this.weather_info = JSON.parse(localStorage.getItem(query));//Recover query result from local storage instead of make another call to the API
      } else {
        fetch(`${this.url_base}weather?q=${query}&units=metric&APPID=${this.api_key}`)
        .then(res=>{
          return res.json();
        }).then(this.setResults)
      }        
    },
    setResults (results) {
      this.weather_info = results;
      localStorage.setItem(this.location, JSON.stringify(this.weather_info));//Save query result to avoid unnecessary api calls
    },
    getFormatedDate() {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let daysOfWeek = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"];

      let day = daysOfWeek[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()]
      let year = d.getFullYear();

      return `${day}, ${month} ${date}${this.getOrdinal(date)} ${year}`
    },
    getOrdinal(d) {
      if (d > 3 && d < 21) return 'th';
      switch (d % 10) {
        case 1:  return "st";
        case 2:  return "nd";
        case 3:  return "rd";
        default: return "th";
      }
    },
    getFormatedTime(unix_timestamp) {
      let date = new Date(unix_timestamp * 1000);
      let hours = date.getHours();
      let minutes = "0" + date.getMinutes();
      return hours + ':' + minutes.substr(-2);
    },
    getFormatedTemp() {
      return this.showF ? Math.round((this.weather_info.main.temp * 9/5) + 32)+'°F' : Math.round(this.weather_info.main.temp)+'°C';
    },
    getCurrentLocation() {
      fetch(`${this.geo_url}?api_key=${this.geo_api_key}`)
      .then(resp=>{
          return resp.json();
        }).then(this.setCurLocation);        
    },
    setCurLocation (currlocation) {
      this.location=currlocation.city+','+currlocation.country_code; //Initializing location
      this.fetchWeatherApi(this.location);      
    }
  },
  beforeMount(){
    this.getCurrentLocation();
  },
  mounted(){

  }
}
</script>

<style>

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Times New Roman', Times, serif;
}

#app {
  background-image: url('./assets/Clear.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.5;
}

#app.Clouds {
  background-image: url('./assets/Clouds.jpg');
}
#app.Clear {
  background-image: url('./assets/Clear.jpg');
}
#app.Thunderstorm {
  background-image: url('./assets/Thunderstorm.jpg');
}
#app.Rain {
  background-image: url('./assets/Rain.jpg');
}
#app.Snow {
  background-image: url('./assets/Snow.jpg');
}
#app.Atmosphere {
  background-image: url('./assets/Atmosphere.jpg');
}
#app.Fog {
  background-image: url('./assets/Fog.jpg');
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 10px;
  color: #b3b3b3;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  background-color: rgba(0,0,0,0.50);
  box-shadow: 0px 0px 8px rgba(0,0,0,0.25);
  border-radius: 15px;
  transition: 0.5s;
}

.search-box .search-bar:focus {
  background-color: rgba(0, 0, 0, 0.500);
  box-shadow: 0px 0px 8px rgba(0,0,0,0.75);
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0,0,0,0.25);
}

.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box{
  text-align: center;
}

.weather-box .temp{
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size:60px;
  font-weight: 1000;
  text-shadow: 3px 6px rgba(0,0,0,0.25);
  background-color: rgba(255,255,255,0.25);
  border-radius: 15px;
  box-shadow: 3px 6px rgba(0,0,0,0.25);
  margin: 30px 0px;
}

.weather-box .weather{
  padding: 10px 25px;
  color: #FFF;
  font-size:40px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0,0,0,0.25);
  margin: 30px 0px;
  display: grid;
  grid-template-columns: auto auto;
}

.set-f-option{
  padding: 10px 25px;
  color: #FFF;
  font-size:28px;
  text-shadow: 3px 6px rgba(0,0,0,0.25);
}
</style>
