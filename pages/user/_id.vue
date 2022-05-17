<template>
  <div>

    <!-- S: Navbar -->
    <navbar />
    <!-- E: Navbar -->

    <!-- S: Hero -->
    <hero title="Lihat Pengguna" />
    <!-- E: Hero -->

    <!-- S: Detail User -->
    <detail-user :user="user"/>
    <!-- E: Detail User -->

    <!-- S: Table Post -->
    <table-post :items="posts" :user="user" @getPosts="getUser()"/>
    <!-- E: Table Post -->

  </div>
</template>

<script>
export default {
  name: "userId",
  data: () => ({
    id: '',
    user: { },
    posts: [],
  }),
  methods: {
    getUser() {
      this.$axios.$get('https://gorest.co.in/public/v2/users/' + this.id, {
        headers: {
          'Authorization': 'Bearer ' + this.$store.state.security.token,
        }
      }).then(response => {
        this.user = response;
        this.user.posts = []
        this.getPosts();
      });
    },
    getPosts() { 
      this.$axios.$get('https://gorest.co.in/public/v2/users/' + this.id + '/posts', {
        headers: {
          'Authorization': 'Bearer ' + this.$store.state.security.token,
        }
      }).then(response => {
        this.posts = response;
        console.log(this.posts)
      });
    }
  },
  created() {
    this.id = this.$route.params.id;
    this.getUser();
  }
}
</script>

<style scoped>

</style>
