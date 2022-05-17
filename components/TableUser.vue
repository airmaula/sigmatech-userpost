<template>
<div>
  <b-container fluid="sm">

    <div class="d-flex justify-content-end">
      <b-button variant="outline-primary" squared small v-b-modal.modal-1>New User</b-button>
    </div>

    <b-table responsive="sm" striped hover :busy="isBusy" :items="items" :fields="fields" :per-page="perPage" :current-page="currentPage" id="table-user" caption-top>

      <template #table-caption>List Users</template>

      <template #table-busy>
        <div class="text-center text-danger my-2">
          <b-spinner class="align-middle"></b-spinner>
          <strong>Loading...</strong>
        </div>
      </template>

      <template #cell(action)="data">
        <b-button variant="info" squared small @click.prevent="viewUser(data.item)">View</b-button>
        <b-button variant="outline-warning" @click="editUser(data.item)" squared small>Update</b-button>
        <b-button variant="outline-danger" @click="confirmDelete(data.item)" squared small>Delete</b-button>
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

    <b-modal id="modal-1" title="New Post" hide-footer>

      <form ref="form" @submit.prevent="storeUser">
        <b-form-group
          label="Name"
          label-for="name"
          invalid-feedback="Name is required"
        >
          <b-form-input
            id="name"
            v-model="name"
            placeholder="Name"
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group
          label="Email"
          label-for="email"
          invalid-feedback="Email is required"
        >
          <b-form-input
            id="email"
            v-model="email"
            placeholder="Email"
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group label="Gender" v-slot="{ ariaDescribedby }">
          <b-form-radio v-model="gender" :aria-describedby="ariaDescribedby" name="gender" value="male">Male</b-form-radio>
          <b-form-radio v-model="gender" :aria-describedby="ariaDescribedby" name="gender" value="female">Female</b-form-radio>
        </b-form-group>

        <b-form-group label="Status" v-slot="{ ariaDescribedby }">
          <b-form-radio v-model="status" :aria-describedby="ariaDescribedby" name="status" value="active">Active</b-form-radio>
          <b-form-radio v-model="status" :aria-describedby="ariaDescribedby" name="status" value="inactive">Inactive</b-form-radio>
        </b-form-group>

        <b-button type="submit" variant="primary" squared small class="mt-3">Save User</b-button>

      </form>

    </b-modal>

    <b-modal id="modal-2" title="Delete Post" hide-footer>

      <p class="mb-3">Do you want to delete this user?</p>
      <b-button @click="deleteUser" variant="outline-danger" class="mr-2" squared small>Yes, delete now</b-button>
      <b-button @click="$bvModal.hide('modal-2')" variant="danger" squared small>Cancel</b-button>

    </b-modal>

  </b-container>
</div>
</template>

<script type="ts">
export default {
  name: "TableUser",
  data: () => ({
    name: '',
    email: '',
    gender: 'male',
    userSelected: false,
    status: 'active',
    genderOption: ['male', 'female'],
    statusOption: ['active', 'inactive'],
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
      }).catch(err => {
        alert('REST API Endpoint ' + err.request.statusText)
      });
    },
    viewUser(data) {
      this.$router.push('/user/' + data.id);
    },
    storeUser() {

      let payload = {
        name: this.name,
        email: this.email,
        status: this.status,
        gender: this.gender,
      };

      if (this.userSelected) {
        this.updateUser(payload);
        return;
      }

      this.$axios.$post('https://gorest.co.in/public/v2/users', payload,{
        headers: {
          'Authorization': 'Bearer ' + this.$store.state.security.token,
        }
      }).then(response => {
        this.getData();
        this.name = '';
        this.email = '';
        this.status = '';
        this.gender = '';
        this.$bvModal.hide('modal-1')
      }).catch(err => {
        alert('REST API Endpoint ' + err.request.statusText)
      });

    },
    confirmDelete(item) {
      this.userSelected = item;
      this.$bvModal.show('modal-2');
    },
    deleteUser() {
      this.$axios.$delete('https://gorest.co.in/public/v2/users/' + this.userSelected.id,{
        headers: {
          'Authorization': 'Bearer ' + this.$store.state.security.token,
        }
      }).then(response => {
        this.getData();
        this.$bvModal.hide('modal-2')
      }).catch(err => {
        alert('REST API Endpoint ' + err.request.statusText)
      });
    },
    editUser(item) {
      this.userSelected = item;
      this.name = item.name;
      this.email = item.email;
      this.status = item.status;
      this.gender = item.gender;
      this.$bvModal.show('modal-1');
    },
    updateUser(payload) {
      this.$axios.$put('https://gorest.co.in/public/v2/users/' + this.userSelected.id, payload,{
        headers: {
          'Authorization': 'Bearer ' + this.$store.state.security.token,
        }
      }).then(response => {
        this.getData();
        this.name = '';
        this.email = '';
        this.status = '';
        this.gender = '';
        this.$bvModal.hide('modal-1')
      }).catch(err => {
        alert('REST API Endpoint ' + err.request.statusText)
      });

      this.userSelected = false;
    }
  },
  created() {
    this.getData();
  }
}
</script>

<style scoped>

</style>
