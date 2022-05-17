<template>
<div>
{{id}}
</div>
</template>

<script>
export default {
  name: "_id",
  data: () => ({
    id: ''
  }),
  created() {
    this.id = this.$route.params.id;
  }
}
</script>

<style scoped>

</style>
