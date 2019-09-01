<template>
  <div id="app">
    <div id="nav">
      <!-- <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link> -->
    </div>
    <router-view/>
  </div>
</template>

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
