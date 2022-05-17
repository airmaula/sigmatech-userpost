<template>
  <div>
      <b-jumbotron>
        <b-container fluid="sm">
          <h1 v-text="title"></h1>
        </b-container>
      </b-jumbotron>
  </div>
</template>

<script type="tsx">
export default {
  name: "Hero",
  props: ['title', 'subtitle']
}
</script>

<style scoped>

</style>
