<template>
  <div class="wrapper d-flex flex-column">
    <Home />
    <Footer />
  </div>
</template>

<script>
import Home from "./components/Home.vue";
import Footer from "./components/Footer.vue";

export default {
  name: "App",
  components: {
    Home,
    Footer,
  },
  created() {
    document.title = "GitHub Repositórios";
  },
};
</script>

<style>
.wrapper {
  min-height: 100vh;
}
</style>
