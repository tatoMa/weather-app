<template>
  <carousel :perPage=1 ref="carousel">
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
    goToPage: {

    },
  },
  components: {
    Weather,
    Carousel,
    Slide,
  },
  watch: {
    goToPage() {
      this.onGoToPage(this.goToPage);
    },
  },
  methods: {
    onGoToPage(page) {
      this.$refs.carousel.goToPage(page);
    },
  },
};
</script>
