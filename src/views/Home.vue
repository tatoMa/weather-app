<template>
  <div>
    <div v-if="search">
      <div v-for="city in search" :key="city.woeid">
        {{city.title}}
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
      searchText: 'new',
    };
  },
  async mounted() {
    const result = await fetch('https://cors-anywhere.herokuapp.com/https://www.metaweather.com/api/location/44418/')
      .then(data => data.json()
        .then((res) => {
          console.log(res);
          this.result = res;
        }));
    await fetch(`https://cors-anywhere.herokuapp.com/https://www.metaweather.com/api/location/search/?query=${this.searchText}`)
      .then(data => data.json()
        .then((res) => {
          console.log(res);
          this.search = res;
        }));
  },
};
</script>

<style lang="scss" scoped>
.icon{
  width: 50px;
  height: auto;
}
</style>
