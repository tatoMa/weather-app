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
  components: {
    Weather,
    Carousel,
    Slide,
  },
  data() {
    return {
      cityList: [
        { woeid: 1105779, title: 'Sydney' },
        { woeid: 1103816, title: 'Melbourne' },
        { woeid: 2151330, title: 'Beijing' },
        { woeid: 2459115, title: 'New York' },
      ],
    };
  },
};
</script>
