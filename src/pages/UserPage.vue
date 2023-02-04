<template>

  <p>{{users}}</p>
  <q-markup-table>
    <q-tr>
      <q-th>ID</q-th>
      <q-th>Name</q-th>
      <q-th>Last name</q-th>
      <q-th>Username</q-th>
      <q-th>Email</q-th>
      <q-th>Role</q-th>
      <q-th>Actions</q-th>
    </q-tr>
    <q-tr v-for="user in users" :key="user.id" class="text-center">
      <q-td>{{ user.id }}</q-td>
      <q-td>{{ user.first_name || "No name" }}</q-td>
      <q-td>{{ user.last_name1 || "No" }}</q-td>
      <q-td>{{ user.username }}</q-td>
      <q-td>{{ user.email }}</q-td>
      <q-td>{{ user.role }}</q-td>
      <q-td>
        <q-btn color="primary" icon="edit" />
        <q-btn color="negative" icon="delete" />
      </q-td>
    </q-tr>
  </q-markup-table>
</template>

<script>
export default {
  name: "UserPage",
  methods: {
    async getUsers() {
      const response = await fetch("http://localhost:8000/users", {
        method: "GET",
      });
      let userMap = await response.json();
      console.log(userMap)
      this.users = userMap.map((user) => {
        return {
          id: user.id,
          first_name: user.first_name,
          last_name1: user.last_name1,
          username: user.username,
          email: user.email,
          role: user.role,
        };
      });
      }
    },
  data() {
    return {
      users: []
    };
  },
  mounted() {
    this.getUsers();
  }
}
</script>

<style scoped>

</style>
