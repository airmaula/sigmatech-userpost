<template>
  <div>
    <b-container fluid="sm">

      <table>
        <tbody>
          <tr>
            <td>Name</td>
            <td>:</td>
            <td>{{ user.name }}</td>
          </tr>
          <tr>
            <td>Gender</td>
            <td>:</td>
            <td>
              <b-badge pill variant="dark" v-if="user.gender === 'male'">Male</b-badge>
              <b-badge pill variant="light" v-else-if="user.gender === 'female'">Female</b-badge>
              <span v-else>{{ user.gender }}</span>
            </td>
          </tr>
          <tr>
            <td>Email</td>
            <td>:</td>
            <td>{{ user.email }}</td>
          </tr>
        </tbody>
      </table>

    </b-container>
  </div>
</template>

<script>
export default {
  name: "DetailUser",
  props: ['user']
}
</script>

<style scoped>
  table {
    margin-bottom: 2rem;
  }
  td {
    padding-right: 1rem;
  }
</style>
