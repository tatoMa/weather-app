<template>
  <div>
    <section class="hero is-fullheight is-dark" v-bind:style="{ backgroundImage: 'url(' + imageCityURL.large2x + ')' }">
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
      </div>
      <!-- body section -->
      <div class="hero-body">
        <div class="container has-text-centered">
          <div class="field has-addons">
          <div class="control is-expanded">
            <input
            class="input"
            type="text"
            placeholder="search a place"
            v-model="searchText"
            @keyup.enter="searchCity"
            >
          </div>
          <div class="control">
            <a class="button is-info" @click="searchCity">
              Search
            </a>
          </div>
        </div>
        <nav class="panel" v-if="search">
          <a class="panel-block"
          v-for="city in search"
          :key="city.woeid"
          v-on:click="weatherDataFromCity(city)"
          >
            {{city.title}}
          </a>
        </nav>
        <!-- <div v-if="search">
          <div v-for="city in search" :key="city.woeid">
            <div v-on:click="weatherDataFromCity(city)">
              {{city.title}}
            </div>
          </div>
        </div> -->
          <div v-if="result.consolidated_weather" class="main-info">
            <p class="title is-2">
              {{Math.round(result.consolidated_weather[0].the_temp)}}°C
            </p>
            <p class="subtitle is-4">
              {{result.title}}
            </p>
          </div>
        </div>
      </div>
      <!-- footer section -->
      <div class="hero-foot bottom-info">
        <nav class="tabs is-boxed is-fullwidth">
          <div class="container">
            <ul>
              <!-- <li class="is-active"> -->
              <li
              v-for="daily in result.consolidated_weather"
              :key="daily.applicable_date.id"
              class="daily-info"
              >
                <div class="has-text-black-bis subtitle is-6">
                  <div>
                    {{new Date(daily.applicable_date).getDate()}}
                  </div>
                  <img class="icon" :src="`https://www.metaweather.com/static/img/weather/${daily.weather_state_abbr}.svg`" alt="">
                  <div>
                    {{Math.round(daily.min_temp)}}°C -
                    {{Math.round(daily.max_temp)}}°C
                  </div>
                </div>
              </li>
            </ul>
          </div>
        </nav>
      </div>
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
      result: {},
      search: {},
      searchText: '',
      resultCity: '',
      imageCityURL: '',
    };
  },
  mounted() {
    this.weatherDataFromCity({ woeid: 1103816 });
    this.cityImageFetchByPexels('melbourne');
    // this.searchCity();
  },
  methods: {
    async searchCity() {
      await fetch(`https://cors-anywhere.herokuapp.com/https://www.metaweather.com/api/location/search/?query=${this.searchText}`)
        .then(data => data.json()
          .then((res) => {
            console.log(res);
            this.search = res;
          }));
    },
    weatherDataFromCity(WeatherDataFromCity) {
      this.search = {};
      console.log(WeatherDataFromCity);
      const { woeid, title } = WeatherDataFromCity;
      fetch(`https://cors-anywhere.herokuapp.com/https://www.metaweather.com/api/location/${woeid}/`)
        .then(data => data.json()
          .then((res) => {
            console.log(res);
            this.result = res;
            this.cityImageFetchByPexels(title);
          }));
    },
    cityImageFetchByPexels(locationName) {
      fetch(`https://api.pexels.com/v1/search?query=${locationName}+query&per_page=1`, {
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
              console.log('src');
              console.log(data.photos[0].src);
              if (data.photos[0].src) {
                this.imageCityURL = data.photos[0].src;
                console.log(data.photos[0].src);
              }
            });
          },
        )
        .catch((err) => {
          console.log('Fetch Error :-S', err);
        });
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
.hero {
  background-color: rgba(88, 88, 88, 0.61);
  background-size: cover;
  background-position: bottom;
}
.main-info, .hero-head{
  background-color: rgba(0, 0, 0, 0.5);
}
.bottom-info{
  background-color: white;
  cursor: pointer;
}
.daily-info{
  padding: 12px 0px;
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
