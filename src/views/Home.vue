<template>
  <div>
    <section
    class="hero is-fullheight is-dark"
    v-bind:style="{
      // eslint-disable-next-line max-len
      backgroundImage: 'linear-gradient(to bottom, rgba(0,0,0,0.4) 5%,rgba(0,0,0,0) 100%), url(' + imageCityURL.large2x + ')' }"
    >

      <!-- navbar -->
      <div class="hero-head">
        <nav class="navbar">
          <div class="container">
            <div class="navbar-brand">
              <a class="navbar-item">
                <p class="title is-4">weather</p>
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
          <div v-if="result.consolidated_weather" class="main-info city-name">
            <div class="subtitle is-5">
              {{getWeekDayText(result.consolidated_weather[0].applicable_date)}},
              {{new Date(result.consolidated_weather[0].applicable_date).getDate()}}
              {{getMonthText(result.consolidated_weather[0].applicable_date)}}
            </div>
            <p class="title is-4">
              {{result.title}}
            </p>
            <div class="temp-toggle">
              <div class="field">
                <b-switch
                v-model="tempUnit"
                type="is-info"
                >
                    {{tempUnit? '°C' : '°F'}}
                </b-switch>
              </div>
            </div>
          </div><!-- location and weather info for top section -->

          <!-- search input and button -->
          <div class="field has-addons">
            <div
            class="control is-expanded"
            :class="loadingSearchCity ? 'is-loading' : ''"
            >
              <input
              class="input"
              type="text"
              placeholder="search a place"
              v-model="searchText"
              @keyup.enter="searchCity"
              >
            </div>
            <div
            class="control"
            :class="loadingSearchCity ? 'is-loading' : ''"
            >
              <a
              class="button is-info"
              @click="searchCity"
              :disabled="loadingSearchCity ? true : false"
              >
                Search
              </a>
            </div>
          </div><!-- search input and button -->

          <!-- search result panel -->
          <nav class="panel" v-if="search">
            <a class="panel-block"
            v-for="city in search"
            :key="city.woeid"
            v-on:click="weatherDataFromCity(city)"
            >
              {{city.title}}
            </a>
          </nav><!-- search result panel -->

          <!-- weather info from result -->
          <div v-if="result.consolidated_weather" class="main-info">
            <p class="title is-1">
              {{
                tempUnit?
                Math.round(result.consolidated_weather[0].the_temp)
                : (Math.round(result.consolidated_weather[0].the_temp * 9/5 + 32))
              }}
              {{tempUnit? '°C' : '°F'}}
            </p>
            <p class="subtitle is-3">
              {{result.consolidated_weather[0].weather_state_name}}
            </p>
          </div><!-- weather info from result -->

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
              v-for="daily in result.consolidated_weather"
              :key="daily.applicable_date.id"
              class="daily-info"
              @click="isCardModalActive = true"
              >
                <div class="has-text-black-bis subtitle is-6">
                  <div class="has-text-weight-semibold">
                    {{getWeekDayText(daily.applicable_date).slice(0, 3)}}
                  </div>
                  <img class="icon" :src="`https://www.metaweather.com/static/img/weather/${daily.weather_state_abbr}.svg`" alt="">
                  <div>
                    <!-- {{Math.round(daily.min_temp)}}°C - -->
                    <!-- {{Math.round(daily.max_temp)}}°C -->
                    {{
                      tempUnit?
                      Math.round(daily.max_temp)
                      : (Math.round(daily.max_temp * 9/5 + 32))
                    }}
                    {{tempUnit? '°C' : '°F'}}
                  </div>
                </div>
              </li>
            </ul>
          </div><!-- 6 days of weather forcast -->

          <!-- card for daily weather info -->
          <b-modal :active.sync="isCardModalActive" :width="640" scroll="keep">
            <div class="card">
                <div class="card-content">

                      <div class="menu si-dark">
                        <p class="menu-label">
                          Transactions
                        </p>
                        <ul class="menu-list">
                          <li><a>Payments</a></li>
                          <li><a>Transfers</a></li>
                          <li><a>Balance</a></li>
                        </ul>
                      </div>

                </div>
            </div>
          </b-modal><!-- card for daily weather info -->

        </nav>
      </div><!-- footer section -->

    </section>
    <!-- {{imageCityURL.original}} -->
    <!-- <img v-if="imageCityURL" :src="imageCityURL.large2x" alt=""> -->
    {{searchText}}
    <input v-model="searchText">
    <button v-on:click="searchCity">SEARCH</button>
    <div v-if="search">
      <div v-for="city in search" :key="city.woeid">
        <div v-on:click="weatherDataFromCity(city)">
          {{city.title}}
        </div>
      </div>
    </div>
    <br>
    <!-- <Weather/> -->
    <div v-if="result">
    {{result.title}}
    {{new Date(result.time).getMonth()+1}} -
    {{new Date(result.time).getDate()}}
    <hr>
    <!-- {{result.consolidated_weather}} -->
      <div v-for="daily in result.consolidated_weather" :key="daily.applicable_date.id">
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
    </div>
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
      isCardModalActive: false,
      tempUnit: true,
      result: {},
      search: {},
      searchText: '',
      resultCity: '',
      imageCityURL: '',
      loadingSearchCity: '',
    };
  },
  created() {
    this.weatherDataFromCity({ woeid: 1103816 });
    this.cityImageFetchByPexels('melbourne');
    // this.searchCity();
  },
  methods: {
    searchCity() {
      this.loadingSearchCity = true;
      fetch(`https://cors-anywhere.herokuapp.com/https://www.metaweather.com/api/location/search/?query=${this.searchText}`)
        .then(data => data.json()
          .then((res) => {
            console.log(res);
            this.search = res;
            this.loadingSearchCity = false;
          }));
    },
    weatherDataFromCity(WeatherDataFromCity) {
      this.loadingSearchCity = true;
      this.search = {};
      this.searchText = '';
      console.log(WeatherDataFromCity);
      const { woeid, title } = WeatherDataFromCity;
      fetch(`https://cors-anywhere.herokuapp.com/https://www.metaweather.com/api/location/${woeid}/`)
        .then(data => data.json()
          .then((res) => {
            console.log(res);
            this.result = res;
            this.cityImageFetchByPexels(title);
            this.loadingSearchCity = false;
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
.icon{
  width: 30px;
  height: auto;
  margin: 6px 0px;
}
.input:active, .input:focus{
  border-color: #3d3d3d;
}
.hero {
  background-color: rgba(88, 88, 88, 0.61);
  background-size: cover;
  background-position: center;
}
.hero-body{
  // align-items:flex-start!important;
}
.main-info, .hero-head{
  background-color: rgba(0, 0, 0, 0.5);
}
.city-name{
  // margin-bottom: 20vh;
  background-color: rgba(0, 0, 0, 0.0);
  position: relative;
  top: -20vh;
}
.temp-toggle{
  position: absolute;
  right: 1.5vh;
  top: 2.5vh;
}
.bottom-info{
  background-color: white;
  cursor: pointer;
}
.daily-info{
  padding: 12px 0px;
  color:rgb(30, 40, 50);
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
</style>
