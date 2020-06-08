<template>
  <div id="app">
    <div id="home">
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-10">
            <div class="card">
              <Home />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import 'bootstrap/dist/css/bootstrap.min.css';
import '@fortawesome/fontawesome-free/css/all.css';
import './assets/main.scss';
import Home from './components/Home.vue';

export default {
  name: 'App',
  components: {
    Home,
  },
};
</script>

<style lang="scss" scoped>
#home {
  padding: 80px 0;
}
</style>
