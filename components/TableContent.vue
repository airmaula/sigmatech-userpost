<template>
<div>
  <b-container fluid="sm">
    <b-table responsive="sm" striped hover :busy="isBusy" :items="items" :fields="fields">

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

    </b-table>
  </b-container>
</div>
</template>

<script type="ts">
export default {
  name: "TableContent",
  data: () => ({
    isBusy: false,
    // fields: ['id', 'name', 'email', 'gender', 'status'],
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
  methods: {
    getData() {
      this.isBusy = true;
      this.$axios.$get('https://gorest.co.in/public/v2/users').then(response => {
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
