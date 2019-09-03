<template>
  <div id="app">
    <navbar/>
    <router-view/>
  </div>
</template>

<script>
import navbar from './components/navbar.vue';

export default {
  components: {
    navbar,
  },
};
</script>
<style lang="scss">
$color-main-background: #222;
$color-snow-cloud: #CCC;
$color-cloud: #00A0B0;

body {
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
