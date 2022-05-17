<template>
<div>
  <b-container fluid="sm">
    <b-table responsive="sm" striped hover :busy="isBusy" :items="items" :fields="fields" :per-page="perPage" :current-page="currentPage" id="table-user">

      <template #table-busy>
        <div class="text-center text-danger my-2">
          <b-spinner class="align-middle"></b-spinner>
          <strong>Loading...</strong>
        </div>
      </template>

      <template #cell(action)="data">
        <b-button variant="info" squared small @click.prevent="viewUser(data.item)">View</b-button>
        <b-button variant="outline-warning" squared small>Update</b-button>
        <b-button variant="outline-danger" squared small>Delete</b-button>
      </template>

      <template #cell(id)="data">
        #{{data.item.id}}
      </template>

    </b-table>

    <b-pagination
      v-model="currentPage"
      :total-rows="rows"
      :per-page="perPage"
      aria-controls="table-user"
    ></b-pagination>

  </b-container>
</div>
</template>

<script type="ts">
export default {
  name: "TableUser",
  data: () => ({
    isBusy: false,
    perPage: 10,
    currentPage: 1,
    fields: [
      {
        key: 'id',
        label: 'ID',
        sortable: true,
      },
      {
        key: 'name',
        label: 'Name',
        sortable: true,
      },
      {
        key: 'email',
        label: 'Email',
        sortable: true,
      },
      {
        key: 'gender',
        label: 'Gender',
        sortable: true,
      },
      {
        key: 'status',
        label: 'Status',
        sortable: true,
      },
      'action',
    ],
    items: []
  }),
  computed: {
    rows() {
      return this.items.length
    }
  },
  methods: {
    getData() {
      this.isBusy = true;
      this.$axios.$get('https://gorest.co.in/public/v2/users', {
        headers: {
          'Authorization': 'Bearer ' + this.$store.state.security.token,
        }
      }).then(response => {
        this.items = response;
        this.isBusy = false;
      });
    },
    viewUser(data) {
      this.$router.push('/user/' + data.id);
    }
  },
  created() {
    this.getData();
  }
}
</script>

<style scoped>

</style>
