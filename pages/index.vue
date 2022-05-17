<template>
  <div>

    <!-- S: Navbar -->
    <navbar />
    <!-- E: Navbar -->

    <!-- S: Hero -->
    <hero title="User Lists" />
    <!-- E: Hero -->

    <!-- S: Table User -->
    <table-user />
    <!-- E: Table User -->

  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'IndexPage',
})
</script>
