<template>
  <div class="home">
    <Weather/>
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
};
</script>
