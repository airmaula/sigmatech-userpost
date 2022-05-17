<template>
  <div>
    <b-navbar toggleable="lg" type="dark" variant="info">
      <b-container fluid="sm">
        <b-navbar-brand href="#" @click="toHomepage">Daftar Pengguna</b-navbar-brand>

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
