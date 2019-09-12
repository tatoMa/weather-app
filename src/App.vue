<template>
  <div id="app">
    <navbar
    :cityList="cityList"
    @finished="finished"
    />
    <router-view
    :cityList="cityList"
    :goToPage="goToPage"/>
  </div>
</template>

<script>
import navbar from './components/navbar.vue';

export default {
  components: {
    navbar,
  },
  data() {
    return {
      cityList: [
        { woeid: 1105779, title: 'Sydney' },
        { woeid: 1103816, title: 'Melbourne' },
        { woeid: 2151330, title: 'Beijing' },
        { woeid: 2459115, title: 'New York' },
      ],
      goToPage: null,
    };
  },
  methods: {
    finished(page) {
      console.log('page', page);
      this.goToPage = page;
    },
  },
};
</script>
<style lang="scss">
$color-main-background: #222;
$color-snow-cloud: #CCC;
$color-cloud: #00A0B0;

body {
  height: 100vh;
  background-color: $color-main-background;
  font-family: 'Poppins', sans-serif;
  color: $color-snow-cloud;
  a {
    text-decoration: none;
    color: $color-cloud;
  }
  text-align: center;
}

.VueCarousel-pagination{
  position: absolute !important;
  bottom: 98px;
}
</style>
