<template>
  <div class="mb-5">
    <b-navbar toggleable="lg" type="dark" variant="primary" fixed="top">
      <b-container fluid="sm">
        <b-navbar-brand href="#" @click="toHomepage">User Post App</b-navbar-brand>

        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

        <b-collapse id="nav-collapse" is-nav>

        </b-collapse>
      </b-container>
    </b-navbar>
  </div>
</template>

<script>
export default {
  name: "Navbar",
  methods: {
    toHomepage() {
      this.$router.push('/');
    }
  }
}
</script>

<style scoped>

</style>
