<template>
<div id="app" :class="typeof weather_info.main != 'undefined' ? weather_info.weather[0].main : ''">
<main>
  <div class="search-box">
    <input type="text" v-model="location" @keypress="fetchWeather" class="search-bar" placeholder="Type a location..."/>
  </div>
  <div class="weather-wrap" v-if="typeof weather_info.main != 'undefined'">
    <div class="location-box">
      <div class="location">{{ weather_info.name }}, {{ weather_info.sys.country }}</div>
      <div class="date">{{getFormatedDate()}}</div>
    </div>
    <div class="weather-box">
      <div class="temp">{{Math.round(weather_info.main.temp)}}°C</div>
      <div class="weather">
        {{weather_info.weather[0].main}}
        <img :src="url_imgbase + weather_info.weather[0].icon + '@2x.png'"/>
      </div>
    </div>
  </div>
</main>
</div>
</template>

<script>

export default {
  name: 'app',
  data () {
    return {
      api_key: '979aea19483ddbb8ba65e61bdd796d5b',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      url_imgbase: 'https://openweathermap.org/img/wn/',
      location: '',
      weather_info: {}
    }
  },
  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(`${this.url_base}weather?q=${this.location}&units=metric&APPID=${this.api_key}`)
        .then(res=>{
          return res.json();
        }).then(this.setResults);
      }
    },
    setResults (results) {
      this.weather_info = results;
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
    }

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
  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  background-color: rgba(0,0,0,0.15);
  box-shadow: 0px 0px 8px rgba(0,0,0,0.25);
  border-radius: 15px;
  transition: 0.5s;
}

.search-box .search-bar:focus {
  background-color: rgba(0,0,0,0.15);
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
  font-size:100px;
  font-weight: 1000;
  text-shadow: 3px 6px rgba(0,0,0,0.25);
  background-color: rgba(255,255,255,0.25);
  border-radius: 15px;
  box-shadow: 3px 6px rgba(0,0,0,0.25);
  margin: 30px 0px;
}

.weather-box .weather{  
  color: #FFF;
  font-size:50px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0,0,0,0.25);
}
</style>
