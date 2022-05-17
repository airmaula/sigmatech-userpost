<template>
<div>
  <b-container fluid="sm">

      <div class="d-flex justify-content-end">
        <b-button variant="outline-primary" squared small v-b-modal.modal-1>New Post</b-button>
      </div>

      <b-table responsive="sm" striped hover :busy="isBusy" :items="items" :fields="fields" :per-page="perPage" :current-page="currentPage" id="table-user" caption-top>

        <template #table-caption>List Posts</template>

        <template #table-busy>
          <div class="text-center text-danger my-2">
            <b-spinner class="align-middle"></b-spinner>
            <strong>Loading...</strong>
          </div>
        </template>

        <template #cell(action)="data">
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
              rows="3"
              max-rows="6"
              required
            ></b-form-textarea>
          </b-form-group>

          <b-button type="submit" variant="primary" squared small>Save</b-button>

        </form>

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
      'action',
    ],
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
      }).then(response => {
        this.$emit('getPosts', {});
        this.title = '';
        this.body = '';
        this.$bvModal.hide('modal-1')
      });

    }
  },
  created() {

  }
}
</script>

<style scoped>

</style>
