<template>
  <carousel :perPage=1>
    <slide>
      <Weather />
    </slide>
    <slide>
      <Weather />
    </slide>
    <slide>
      <Weather />
    </slide>
    <slide>
      <Weather />
    </slide>
  </carousel>
</template>

<script>
import Weather from '@/components/Weather.vue';
import { Carousel, Slide } from 'vue-carousel';

export default {
  name: 'home',
  components: {
    Weather,
    Carousel,
    Slide,
  },
};
</script>
