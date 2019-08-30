/* eslint-disable max-len */
<template>
  <div>

    <!-- full screen loading spinner -->
    <div :closable="false">
      <b-loading is-full-page :active.sync="loadingWeatherInfo" :can-cancel="true"></b-loading>
    </div><!-- full screen loading spinner -->

    <section
    class="hero is-fullheight is-dark"
    v-bind:style="{
      // eslint-disable-next-line max-len
      backgroundImage: 'linear-gradient(to bottom, rgba(0,0,0,0.4) 10%,rgba(0,0,0,0) 100%), url(' + imageCityURL.large2x + ')' }"
    >

      <!-- navbar -->
      <div class="hero-head">
        <nav class="navbar">
          <div class="container">
            <div class="navbar-brand">
              <a class="navbar-item">
                <p class="title is-4">Beauty Weather</p>
              </a>
              <span class="navbar-burger burger" data-target="navbarMenuHeroB">
                <span></span>
                <span></span>
                <span></span>
              </span>
            </div>
            <div id="navbarMenuHeroB" class="navbar-menu">
              <div class="navbar-end">
                <a class="navbar-item is-active">
                  Home
                </a>
                <a class="navbar-item">
                  Examples
                </a>
                <a class="navbar-item">
                  Documentation
                </a>
                <!-- <span class="navbar-item">
                  <a class="button is-info is-inverted">
                    <span class="icon">
                      <i class="fab fa-github"></i>
                    </span>
                    <span>Download</span>
                  </a>
                </span> -->
              </div>
            </div>
          </div>
        </nav>
      </div><!-- navbar -->

      <!-- body section -->
      <div class="hero-body">
        <div class="container has-text-centered">

          <!-- location and weather info for top section -->
          <div v-if="weatherDataByCitySelected.consolidated_weather" class="main-info city-name">
            <div class="subtitle is-5">
              {{getWeekDayText(weatherDataByCitySelected.consolidated_weather[0].applicable_date)}},
              {{new Date(weatherDataByCitySelected.consolidated_weather[0].applicable_date).getDate()}}
              {{getMonthText(weatherDataByCitySelected.consolidated_weather[0].applicable_date)}}
            </div>
            <p class="title is-4">
              {{weatherDataByCitySelected.title}}
            </p>
            <div class="temp-toggle">
              <div class="field">
                <b-switch
                v-model="isTempUnitCalcius"
                type="is-info"
                >
                    {{isTempUnitCalcius? '°C' : '°F'}}
                </b-switch>
              </div>
            </div>
          </div><!-- location and weather info for top section -->

          <!-- search input and button -->
          <div class="field has-addons">
            <div
            class="control is-expanded"
            :class="loadingCityList ? 'is-loading' : ''"
            >
              <input
              class="input"
              type="text"
              placeholder="Search a city"
              v-model="cityNameByInput"
              @keyup.enter="searchCity"
              >
            </div>
            <div
            class="control"
            :class="loadingCityList ? 'is-loading' : ''"
            >
              <a
              class="button is-info"
              @click="searchCity"
              :disabled="loadingCityList ? true : false"
              >
                Search
              </a>
            </div>
          </div><!-- search input and button -->

          <!-- search weatherDataByCitySelected panel -->
          <nav class="panel" v-if="cityListBySearch">
            <a class="panel-block"
            v-for="city in cityListBySearch"
            :key="city.woeid"
            v-on:click="weatherDataFromCity(city)"
            >
              {{city.title}}
            </a>
          </nav><!-- search weatherDataByCitySelected panel -->

          <!-- weather info from weatherDataByCitySelected -->
          <div v-if="weatherDataByCitySelected.consolidated_weather" class="main-info">
            <p class="title is-1">
              {{
                isTempUnitCalcius?
                Math.round(weatherDataByCitySelected.consolidated_weather[0].the_temp)
                : (Math.round(weatherDataByCitySelected.consolidated_weather[0].the_temp * 9/5 + 32))
              }}
              {{isTempUnitCalcius? '°C' : '°F'}}
            </p>
            <p class="subtitle is-3">
              {{weatherDataByCitySelected.consolidated_weather[0].weather_state_name}}
            </p>
          </div><!-- weather info from weatherDataByCitySelected -->

        </div>
      </div><!-- body section -->

      <!-- footer section -->
      <div class="hero-foot bottom-info">
        <nav class="tabs is-boxed is-fullwidth">

          <!-- 6 days of weather forcast -->
          <div class="container">
            <ul>
              <!-- <li class="is-active"> -->
              <li
              v-for="daily in weatherDataByCitySelected.consolidated_weather"
              :key="daily.applicable_date.id"
              class="daily-info"
              @click="dailyWeatherDetailPopup = true;
              selectedDayWeatherDetail = daily"
              >
                <div class="has-text-black-bis subtitle is-7">
                  <div class="has-text-weight-semibold">
                    {{getWeekDayText(daily.applicable_date).slice(0, 3)}}
                  </div>
                  <img class="icon" :src="`https://www.metaweather.com/static/img/weather/${daily.weather_state_abbr}.svg`" alt="">
                  <div>
                    <!-- {{Math.round(daily.min_temp)}}°C - -->
                    <!-- {{Math.round(daily.max_temp)}}°C -->
                    {{
                      isTempUnitCalcius?
                      Math.round(daily.max_temp)
                      : (Math.round(daily.max_temp * 9/5 + 32))
                    }}
                    {{isTempUnitCalcius? '°C' : '°F'}}
                  </div>
                </div>
              </li>
            </ul>
          </div><!-- 6 days of weather forcast -->

        </nav>
      </div><!-- footer section -->

      <!-- card for daily weather info -->
          <b-modal :active.sync="dailyWeatherDetailPopup" :width="640" scroll="keep">
            <div class="card is-white daily-details">
              <b-menu>
                <b-menu-list label="Daily Details">
                    <b-menu-item :label="`${selectedDayWeatherDetail.applicable_date}`"></b-menu-item>
                    <b-menu-item :label="`Status: ${selectedDayWeatherDetail.weather_state_name}`"></b-menu-item>
                    <b-menu-item :label="`Min Temp: ${Math.round(selectedDayWeatherDetail.min_temp).toString()}°C`"></b-menu-item>
                    <b-menu-item :label="`Max Temp: ${Math.round(selectedDayWeatherDetail.max_temp).toString()}°C`"></b-menu-item>
                    <b-menu-item :label="`Humidity: ${Math.round(selectedDayWeatherDetail.humidity).toString()}`"></b-menu-item>
                    <b-menu-item :label="`Wind Direction: ${Math.round(selectedDayWeatherDetail.wind_direction).toString()}`"></b-menu-item>
                    <b-menu-item :label="`Wind Speed: ${Math.round(selectedDayWeatherDetail.wind_speed).toString()}`"></b-menu-item>
                    <b-menu-item :label="`Air Pressure: ${Math.round(selectedDayWeatherDetail.air_pressure).toString()}`"></b-menu-item>
                    <b-menu-item :label="`Updated: ${selectedDayWeatherDetail.created}`"></b-menu-item>
                </b-menu-list>
                <b-menu-list label="Actions">
                    <b-menu-item label="CLOSE" @click="dailyWeatherDetailPopup = false;"></b-menu-item>
                </b-menu-list>
              </b-menu>
            </div>
          </b-modal><!-- card for daily weather info -->
    </section>
    <!-- {{imageCityURL.original}} -->
    <!-- <img v-if="imageCityURL" :src="imageCityURL.large2x" alt=""> -->
    <!-- {{cityNameByInput}}
    <input v-model="cityNameByInput">
    <button v-on:click="searchCity">SEARCH</button>
    <div v-if="search">
      <div v-for="city in search" :key="city.woeid">
        <div v-on:click="weatherDataFromCity(city)">
          {{city.title}}
        </div>
      </div>
    </div>
    <br> -->
    <!-- <Weather/> -->
    <!-- <div v-if="weatherDataByCitySelected">
    {{weatherDataByCitySelected.title}}
    {{new Date(weatherDataByCitySelected.time).getMonth()+1}} -
    {{new Date(weatherDataByCitySelected.time).getDate()}}
    <hr> -->
    <!-- {{weatherDataByCitySelected.consolidated_weather}} -->
      <!-- <div v-for="daily in weatherDataByCitySelected.consolidated_weather" :key="daily.applicable_date.id">
        <div>
          <img class="icon" :src="`https://www.metaweather.com/static/img/weather/${daily.weather_state_abbr}.svg`" alt="">
        </div>
        <div>
          {{new Date(daily.applicable_date).getMonth()+1}} -
          {{new Date(daily.applicable_date).getDate()}}
        </div>
        <div>
          {{daily.weather_state_name}}
          {{Math.round(daily.the_temp)}}
        </div>
        <div>
        wind:
        <br>
        {{daily.wind_speed.toFixed(2)}} mph
        {{daily.wind_direction.toFixed(0)}} degrees
        </div>
      </div>
    </div>-->
  </div>
</template>

<script>
// @ is an alias to /src
import { createHash } from 'crypto';
import Weather from '@/components/Weather.vue';

export default {
  name: 'home',
  components: {
    Weather,
  },
  data() {
    return {
      dailyWeatherDetailPopup: false,
      isTempUnitCalcius: true,
      weatherDataByCitySelected: {},
      cityListBySearch: {},
      cityNameByInput: '',
      imageCityURL: '',
      loadingCityList: false,
      loadingWeatherInfo: false,
      selectedDayWeatherDetail: {},
    };
  },
  created() {
    this.weatherDataFromCity({ woeid: 1105779 });
    // this.cityImageFetchByPexels('melbourne');
    // this.searchCity();
  },
  methods: {
    searchCity() {
      this.loadingCityList = true;
      fetch(`https://cors-anywhere.herokuapp.com/https://www.metaweather.com/api/location/search/?query=${this.cityNameByInput}`)
        .then(data => data.json()
          .then((res) => {
            console.log(res);
            this.cityListBySearch = res;
            this.loadingCityList = false;
          }));
    },
    weatherDataFromCity(WeatherDataFromCity) {
      this.loadingWeatherInfo = true;
      this.cityListBySearch = {};
      this.cityNameByInput = '';
      const { woeid } = WeatherDataFromCity;
      fetch(`https://cors-anywhere.herokuapp.com/https://www.metaweather.com/api/location/${woeid}/`)
        .then(data => data.json()
          .then((res) => {
            console.log(res);
            this.weatherDataByCitySelected = res;
            this.cityImageFetchByPexels(res.title);
            this.loadingWeatherInfo = false;
          }));
    },
    cityImageFetchByPexels(locationName) {
      fetch(`https://api.pexels.com/v1/search?query=${locationName}+query&per_page=10`, {
        headers: {
          Authorization: 'Bearer ' + '563492ad6f91700001000001caff54fbc73040acae2be7c403e3e8d8',
          // 'Content-Type': 'application/json',
          // 'Content-Type': 'application/x-www-form-urlencoded',
        },
      })
        .then(
          (response) => {
            if (response.status !== 200) {
              console.log(`Looks like there was a problem. Status Code: ${response.status}`);
              return;
            }
            // Examine the text in the response
            response.json().then((data) => {
              if (data.photos.length !== 0) {
                const randomPics = Math.floor(Math.random() * data.photos.length);
                console.log(data.photos);
                if (data.photos[randomPics].src) {
                  this.imageCityURL = data.photos[randomPics].src;
                  console.log(data.photos[randomPics].src);
                }
              }
            });
          },
        )
        .catch((err) => {
          console.log('Fetch Error :-S', err);
        });
    },
    getWeekDayText(day) {
      const weekDay = new Date(day).getDay();
      const weekText = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
      return weekText[weekDay];
    },
    getMonthText(date) {
      const month = new Date(date).getMonth();
      const mlist = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
      return mlist[month];
    },
  },
};
</script>

<style lang="scss" scoped>
.burger{
  color: white;
}
.icon{
  width: 25px;
  height: auto;
  margin: 6px 0px;
}
.input:active, .input:focus{
  border-color: #3d3d3d;
}
.hero {
  background-color: rgba(88, 88, 88, 0.61);
  background-size: cover;
  // background-position: center;
  background-position: 50% 70%;
}
.hero-body{
  // align-items:flex-start!important;
}
.hero-head{
  background-color: rgba(0, 0, 0, 0.5);
}
.main-info{
  text-shadow: 2px 2px 0 rgba(63, 63, 63, 0.5);
  padding-top: 10vh;
}
.city-name{
  // margin-bottom: 20vh;
  background-color: rgba(0, 0, 0, 0.0);
  position: relative;
  top: -20vh;
  color: #FFFFFF;
  text-shadow: 1.5px 1px 0 rgba(63, 63, 63, 0.5);
}
.temp-toggle{
  position: absolute;
  right: 0.5vh;
  top: 12vh;
}
.bottom-info{
  background-color: white;
  cursor: pointer;
}
.daily-info{
  padding: 10px 0px;
  color:rgb(30, 40, 50);
}
.daily-info:first-child {
  // background-color: rgb(223, 223, 223);
}
.daily-info:hover{
  background-color: rgb(211, 211, 211);
}
.panel-block{
  background-color: white;
  &:hover{
    background-color: rgb(211, 211, 211);
  }
}
.daily-details{
  padding-top: 14px;
  padding-bottom: 5px;
}
</style>
