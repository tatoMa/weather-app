<template>
  <carousel :perPage=1>
    <slide v-for="(city, index) in cityList" :key="city.weoid">
      <Weather :city="city" :index="index"/>
    </slide>
  </carousel>
</template>

<script>
import { Carousel, Slide } from 'vue-carousel';
import Weather from '@/components/Weather.vue';

export default {
  name: 'home',
  props: {
    cityList: {
      type: Array,
    },
  },
  components: {
    Weather,
    Carousel,
    Slide,
  }
};
</script>
