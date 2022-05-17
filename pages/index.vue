<template>
  <div>

    <!-- S: Navbar -->
    <navbar />
    <!-- E: Navbar -->

    <!-- S: Wrap Content -->
    <div class="wrap-content">

      <!-- S: Hero -->
      <hero title="Daftar Pengguna" />
      <!-- E: Hero -->

      <!-- S: Table User -->
      <table-user />
      <!-- E: Table User -->

    </div>
    <!-- E: Wrap Content -->

    <!-- S: Footer -->
    <foot />
    <!-- E: Footer -->

  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'IndexPage',
})
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Rubik:wght@400;500;700&display=swap');
* {
  font-family: 'Rubik', sans-serif;
}
.wrap-content {
  min-height: 100vh;
}
</style>
