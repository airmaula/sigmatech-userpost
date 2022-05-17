<template>
  <div>

    <!-- S: Navbar -->
    <navbar />
    <!-- E: Navbar -->

    <!-- S: Hero -->
    <hero title="User Lists" />
    <!-- E: Hero -->

    <!-- S: Table Content -->
    <table-content />
    <!-- E: Table Content -->

  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'IndexPage',
})
</script>
