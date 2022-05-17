<template>
<div>
  <b-container fluid="sm">

      <div class="d-flex justify-content-end">
        <b-button variant="outline-primary" squared small v-b-modal.modal-1>Buat Post</b-button>
      </div>

      <b-table responsive="sm" striped hover :busy="isBusy" :items="items" :fields="fields" :per-page="perPage" :current-page="currentPage" id="table-user" caption-top>

        <template #table-caption>Daftar Post</template>

        <template #table-busy>
          <div class="text-center text-danger my-2">
            <b-spinner class="align-middle"></b-spinner>
            <strong>Loading...</strong>
          </div>
        </template>

        <template #cell(action)="data">
          <b-button variant="outline-danger" squared size="sm" @click="confirmDelete(data.item)">Delete</b-button>
        </template>

        <template #cell(id)="data">
          #{{data.item.id}}
        </template>

      </b-table>

      <div v-if="items.length > 0">
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
      <div v-else>
        <p class="text-muted text-center">Tidak ada post. <a href="#" v-b-modal.modal-1>Buat Post</a></p>
      </div>



    <b-modal id="modal-1" title="New Post" hide-footer>

        <form ref="form" @submit.prevent="storePost">
          <b-form-group
            label="Title"
            label-for="title"
            invalid-feedback="Name is required"
          >
            <b-form-input
              id="title"
              v-model="title"
              placeholder="Title"
              required
            ></b-form-input>
          </b-form-group>

          <b-form-group
            label="Body"
            label-for="body"
            invalid-feedback="Name is required"
          >
            <b-form-textarea
              id="body"
              v-model="body"
              placeholder="Body"
              rows="6"
              max-rows="6"
              required
            ></b-form-textarea>
          </b-form-group>

          <b-button type="submit" variant="primary" squared small class="mt-3">Save Post</b-button>
          <b-button @click="$bvModal.hide('modal-1')" variant="outline-primary" squared small class="mt-3 ml-2">Cancel</b-button>

        </form>

      </b-modal>

    <b-modal id="modal-2" title="Delete Post" hide-footer>

      <p class="mb-3">Do you want to delete this post ?</p>

      <table class="table table-striped">
        <tbody>
        <tr>
          <td>Title</td>
          <td>:</td>
          <td>{{ postSelected.title }}</td>
        </tr>
        </tbody>
      </table>

      <div class="mt-4">
        <b-button @click="deletePost" variant="outline-danger" class="mr-2" squared small>Yes, delete now</b-button>
        <b-button @click="$bvModal.hide('modal-2')" variant="danger" squared small>Cancel</b-button>
      </div>

    </b-modal>


  </b-container>
</div>
</template>

<script type="ts">
export default {
  name: "TableUser",
  props: ['items', 'user'],
  data: () => ({
    title: '',
    body: '',
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
        key: 'title',
        label: 'Title',
        sortable: true,
      },
      {
        key: 'body',
        label: 'Body',
        sortable: true,
      },
      {
        key: 'action',
        thClass: 'text-center',
        tdClass: 'text-center',
      }
    ],
    postSelected: {}
  }),
  computed: {
    rows() {
      return this.items.length
    }
  },
  methods: {
    storePost() {

      let payload = {
        title: this.title,
        body: this.body,
      };

      this.$axios.$post('https://gorest.co.in/public/v2/users/' + this.user.id + '/posts', payload,{
        headers: {
          'Authorization': 'Bearer ' + this.$store.state.security.token,
        }
      }).then(() => {
        this.$emit('getPosts', {});
        this.title = '';
        this.body = '';
        this.$bvModal.hide('modal-1')

        this.$bvToast.toast('Post created successful', {
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
      this.postSelected = item;
      this.$bvModal.show('modal-2');
    },
    deletePost() {
      this.$axios.$delete('https://gorest.co.in/public/v2/users/' + this.user.id + '/posts/' + this.postSelected.id,{
        headers: {
          'Authorization': 'Bearer ' + this.$store.state.security.token,
        }
      }).then(() => {
        this.$emit('getPosts', {});
        this.$bvModal.hide('modal-2')
      }).catch(err => {
        this.$bvToast.toast('REST API Endpoint ' + err.request.statusText, {
          title: 'There are something error',
          variant: 'danger',
          solid: true,
          toaster: 'b-toaster-top-full'
        })
        this.$bvModal.hide('modal-2')
      });
    }
  },
  created() {

  }
}
</script>

<style scoped>

</style>
