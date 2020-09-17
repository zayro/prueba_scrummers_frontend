<template>
  <div>
    <nav class="navbar navbar-dark bg-primary">
      <span class="navbar-brand mb-0 h1">Hotel - CHotel</span>
    </nav>

    <div class="container">
      <div class="center-top">
        <router-view />
      </div>
    </div>
  </div>
</template>


<style lang="scss" scoped>
.md-app {
  height: 100vh;
}

// Demo purposes only
.md-drawer {
  width: 100%;
  //max-width: calc(100vw - 125px);
}

.center-top {
  margin: 0 auto;
  margin-top: 10px;
}
</style>

<script>
export default {
  name: "App",
};
</script>