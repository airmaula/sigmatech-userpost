<template>
<div>
  <b-container fluid="sm">

    <div class="d-flex justify-content-end">
      <b-button variant="outline-primary" squared small @click="newUser">Buat Pengguna</b-button>
    </div>

    <b-table responsive="sm" striped hover :busy="isBusy" :items="items" :fields="fields" :per-page="perPage" :current-page="currentPage" id="table-user" caption-top>

      <template #table-caption>Table Pengguna</template>

      <template #table-busy>
        <div class="text-center text-danger my-2">
          <b-spinner class="align-middle"></b-spinner>
          <strong>Memuat data...</strong>
        </div>
      </template>

      <template #cell(gender)="data">
        <b-badge pill variant="dark" v-if="data.item.gender === 'male'">Male</b-badge>
        <b-badge pill variant="light" v-else>Female</b-badge>
      </template>

      <template #cell(status)="data">
        <b-badge pill variant="info" v-if="data.item.status === 'active'">Active</b-badge>
        <b-badge pill variant="secondary" v-else>Inactive</b-badge>
      </template>


      <template #cell(action)="data">
        <b-button variant="info" squared size="sm" @click.prevent="viewUser(data.item)">View</b-button>
        <b-button variant="outline-warning" @click="editUser(data.item)" squared size="sm">Update</b-button>
        <b-button variant="outline-danger" @click="confirmDelete(data.item)" squared size="sm">Delete</b-button>
      </template>

      <template #cell(id)="data">
        #{{data.item.id}}
      </template>

    </b-table>

    <div class="my-4">
      <b-pagination
        v-model="currentPage"
        :total-rows="rows"
        :per-page="perPage"
        aria-controls="table-user"
        align="right"
        first-text="First"
        prev-text="Prev"
        next-text="Next"
        last-text="Last"
      ></b-pagination>
    </div>

    <b-modal id="modal-1" :title="modalTitle" hide-footer>

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
        <b-button @click="$bvModal.hide('modal-1')" variant="outline-primary" squared small class="mt-3 ml-2">Cancel</b-button>

      </form>

    </b-modal>

    <b-modal id="modal-2" title="Delete User" hide-footer>

      <p class="mb-3">Do you want to delete this user ?</p>

      <table class="table table-striped">
        <tbody>
        <tr>
          <td>Name</td>
          <td>:</td>
          <td>{{ userSelected.name }}</td>
        </tr>
        <tr>
          <td>Gender</td>
          <td>:</td>
          <td>
            <b-badge pill variant="dark" v-if="userSelected.gender === 'male'">Male</b-badge>
            <b-badge pill variant="light" v-else>Female</b-badge>
          </td>
        </tr>
        <tr>
          <td>Email</td>
          <td>:</td>
          <td>{{ userSelected.email }}</td>
        </tr>
        </tbody>
      </table>

      <div class="mt-4">
        <b-button @click="deleteUser" variant="outline-danger" class="mr-2" squared small>Yes, delete now</b-button>
        <b-button @click="$bvModal.hide('modal-2')" variant="danger" squared small>Cancel</b-button>
      </div>

    </b-modal>

  </b-container>
</div>
</template>

<script type="ts">
export default {
  name: "TableUser",
  data: () => ({
    modalTitle: 'New User',
    name: '',
    email: '',
    gender: 'male',
    userSelected: false,
    status: 'active',
    genderOption: ['male', 'female'],
    statusOption: ['active', 'inactive'],
    isBusy: true,
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
        thClass: 'text-center',
        tdClass: 'text-center',
      },
      {
        key: 'status',
        label: 'Status',
        sortable: true,
        thClass: 'text-center',
        tdClass: 'text-center',
      },
      {
        key: 'action',
        thClass: 'text-center',
        tdClass: 'text-center',
      }
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
      this.$axios.$get('https://gorest.co.in/public/v2/users', {
        headers: {
          'Authorization': 'Bearer ' + this.$store.state.security.token,
        }
      }).then(response => {
        this.items = response;

        this.items.sort((a, b) => b.id - a.id);

        this.isBusy = false;
      }).catch(err => {
        this.$bvToast.toast('REST API Endpoint ' + err.request.statusText, {
          title: 'There are something error',
          variant: 'danger',
          solid: true,
          toaster: 'b-toaster-top-full'
        })
      });
    },
    viewUser(data) {
      this.$router.push('/user/' + data.id);
    },
    newUser() {
      this.userSelected = false;
      this.modalTitle = 'New User';
      this.$bvModal.show('modal-1')
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

        this.$bvToast.toast('User created successful', {
          title: 'Success',
          variant: 'success',
          solid: true,
          toaster: 'b-toaster-bottom-right'
        })

      }).catch(err => {
        this.$bvToast.toast('REST API Endpoint ' + err.request.statusText, {
          title: 'There are something error',
          variant: 'danger',
          solid: true,
          toaster: 'b-toaster-top-full'
        })
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
        this.$bvToast.toast('User deleted successful', {
          title: 'Success',
          variant: 'success',
          solid: true,
          toaster: 'b-toaster-bottom-right'
        })
      }).catch(err => {
        this.$bvToast.toast('REST API Endpoint ' + err.request.statusText, {
          title: 'There are something error',
          variant: 'danger',
          solid: true,
          toaster: 'b-toaster-top-full'
        })
      });
    },
    editUser(item) {
      this.userSelected = item;
      this.name = item.name;
      this.email = item.email;
      this.status = item.status;
      this.gender = item.gender;
      this.modalTitle = 'Edit User';
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
        this.$bvToast.toast('User updated successful', {
          title: 'Success',
          variant: 'success',
          solid: true,
          toaster: 'b-toaster-bottom-right'
        })
        this.userSelected = false;
      }).catch(err => {
        this.$bvToast.toast('REST API Endpoint ' + err.request.statusText, {
          title: 'There are something error',
          variant: 'danger',
          solid: true,
          toaster: 'b-toaster-top-full'
        })
        this.userSelected = false;
      });
    }
  },
  created() {
    this.getData();
  }
}
</script>

<style>

</style>
