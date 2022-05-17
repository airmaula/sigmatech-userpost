<template>
  <div>

    <!-- S: Navbar -->
    <navbar />
    <!-- E: Navbar -->

    <!-- S: Hero -->
    <hero title="Lihat Pengguna" />
    <!-- E: Hero -->

    <div v-if="notFound">
        <b-container fluid="sm">
          <p>User not found</p>
          <b-button type="submit" variant="outline-primary" @click.prevent="$router.push('/')" squared small>Back to home</b-button>
        </b-container>
    </div>

    <div v-else>

      <!-- S: Detail User -->
      <detail-user :user="user"/>
      <!-- E: Detail User -->

      <!-- S: Table Post -->
      <table-post :items="posts" :user="user" @getPosts="getUser()"/>
      <!-- E: Table Post -->
    </div>


  </div>
</template>

<script>
export default {
  name: "userId",
  data: () => ({
    id: '',
    user: { },
    posts: [],
    notFound: false,
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
      }).catch(err => {
        this.$bvToast.toast('REST API Endpoint ' + err.request.statusText, {
          title: 'There are something error',
          variant: 'danger',
          solid: true,
          toaster: 'b-toaster-top-full'
        })
        this.notFound = true;
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
      }).catch(err => {
        this.$bvToast.toast('REST API Endpoint ' + err.request.statusText, {
          title: 'There are something error',
          variant: 'danger',
          solid: true,
          toaster: 'b-toaster-top-full'
        })
        this.notFound = true;
      });
    }
  },
  created() {
    this.id = this.$route.params.id;
    this.getUser();
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Rubik:wght@400;500;700&display=swap');
* {
  font-family: 'Rubik', sans-serif;
}
</style>
