<template>
  <div>
    <section class="hero is-fullheight is-info" v-bind:style="{ backgroundImage: 'url(' + imageCityURL.large2x + ')' }">
      <div class="hero-head">
        <nav class="navbar">
          <div class="container">
            <div class="navbar-brand">
              <a class="navbar-item">
                <img src="https://bulma.io/images/bulma-type-white.png" alt="Logo">
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

      <div class="hero-body">
        <div class="container has-text-centered">
          <div class="field has-addons">
          <div class="control is-expanded">
            <input class="input" type="text" placeholder="Find a repository">
          </div>
          <div class="control">
            <a class="button is-info">
              Search
            </a>
          </div>
        </div>
          <div v-if="result">
            <p class="title" >
              {{Math.round(result.consolidated_weather[0].the_temp)}}
            </p>
            <p class="subtitle">
              {{result.title}}
            </p>
          </div>
        </div>
      </div>

      <div class="hero-foot">
        <nav class="tabs is-boxed is-fullwidth">
          <div class="container">
            <ul>
              <!-- <li class="is-active"> -->
              <li v-for="daily in result.consolidated_weather" :key="daily.applicable_date.id">
                <a>
                  <img class="icon" :src="`https://www.metaweather.com/static/img/weather/${daily.weather_state_abbr}.svg`" alt="">

                  {{new Date(daily.applicable_date).getMonth()+1}} -
                  {{new Date(daily.applicable_date).getDate()}}
                  <br>
                  {{daily.weather_state_name}}
                  {{Math.round(daily.the_temp)}}°C
                  <br>
                  wind:
                  {{daily.wind_speed.toFixed(2)}} mph
                  {{daily.wind_direction.toFixed(0)}}°
                </a>
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
        <div v-on:click="weatherDataFromCity(city.woeid)">
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
        {{new Date(daily.applicable_date).getMonth()+1}} -
        {{new Date(daily.applicable_date).getDate()}}
        {{daily.weather_state_name}}
        {{Math.round(daily.the_temp)}}
        <br>
        wind:
        <br>
        {{daily.wind_speed.toFixed(2)}} mph
        {{daily.wind_direction.toFixed(0)}} degrees
        <br>
        <img class="icon" :src="`https://www.metaweather.com/static/img/weather/${daily.weather_state_abbr}.svg`" alt="">
        <hr>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
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
  async mounted() {
    this.weatherDataFromCity(1103816);
    this.cityImageFetchByPexels();
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
    async weatherDataFromCity(WeatherDataFromCity) {
      const result = await fetch(`https://cors-anywhere.herokuapp.com/https://www.metaweather.com/api/location/${WeatherDataFromCity}/`)
        .then(data => data.json()
          .then((res) => {
            console.log(res);
            this.result = res;
          }));
    },
    async cityImageFetchByPexels() {
      await fetch('https://api.pexels.com/v1/search?query=melbourne+query&per_page=1', {
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
              this.imageCityURL = data.photos[0].src;
              console.log(data.photos[0].src);
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
  width: 50px;
  height: auto;
  position: absolute;
  z-index: 10;
  transform: translate(0,- 100%)
}
.hero {
  background-color: rgba(88, 88, 88, 0.61);
  background-size: cover;
  background-position: bottom;
}
</style>
