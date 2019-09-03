<template>
    <nav class="navbar is-dark">
          <div class="container">
            <div class="navbar-brand">
              <a class="navbar-item">
                <p class="title is-4 has-text-light">Beauty Weather</p>
              </a>
              <span
              class="navbar-burger burger"
              data-target="navbarMenuHeroB"
              :class="burgerBar ? 'is-active' : ''"
              v-on:click="toggleBurgerBar">
                <span></span>
                <span></span>
                <span></span>
              </span>
            </div>
            <div id="navbarMenuHeroB"
            class="navbar-menu"
            :class="burgerBar ? 'is-active' : ''"
            >
              <div class="navbar-end">
                <a class="navbar-item is-active">
                  {{cityList[0].title}}
                </a>
                <a class="navbar-item">
                  {{cityList[1].title}}
                </a>
                <a class="navbar-item">
                  {{cityList[2].title}}
                </a>
                <a class="navbar-item">
                  {{cityList[3].title}}
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
</template>

<script>
export default {
  props: {
    cityList: {
      type: Array,
    },
  },
  data() {
    return {
      burgerBar: false,
    };
  },
  methods: {
    toggleBurgerBar() {
      this.burgerBar = !this.burgerBar;
      console.log('toggled');
    },
  },
};
</script>
