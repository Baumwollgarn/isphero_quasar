<template>
  <div class="q-pa-md">
  <q-markup-table>
    <q-tr>
      <q-th colspan="7">
        <div class="row no-wrap items-center">
          <div class="col">
            <q-input
              v-model="search"
              debounce="500"
              rounded
              dense
              placeholder="Search"
              color="blue-5"
              inverted
              class="text-white"
              @update:model-value="filterUsers"
            >
              <template v-slot:before>
                <q-icon name="person" />
              </template>
            </q-input>
          </div>
        </div>
      </q-th>
    </q-tr>
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
      <q-td>{{ user.first_name }}</q-td>
      <q-td>{{ user.last_name1 }}</q-td>
      <q-td>{{ user.username }}</q-td>
      <q-td>{{ user.email }}</q-td>
      <q-td>{{ user.role }}</q-td>
      <q-td>
        <q-btn color="primary" icon="edit" />
        <q-btn color="negative" icon="delete" />
      </q-td>
    </q-tr>
  </q-markup-table>
  </div>
</template>

<script>
export default {
  name: "UserPage",
  data() {
    return {
      search: "",
      users: [],
      usersFiltered: [],
    };
  },
  methods: {
    async getUsers() {
      const response = await fetch("http://localhost:8000/users", {
        method: "GET",
      });
      let userMap = await response.json();
      this.users = userMap.map((user) => {
        return {
          id: user.id,
          first_name: user.user_data ? user.user_data.first_name : " - ",
          last_name1: user.user_data ? user.user_data.last_name1 : " - ",
          username: user.username,
          email: user.email,
          role: user.role,
        };
      });
    },
    filterUsers() {
      this.usersFiltered = this.users.filter((user) => {
        return (
          user.first_name.toLowerCase().includes(this.search.toLowerCase()) ||
          user.last_name1.toLowerCase().includes(this.search.toLowerCase()) ||
          user.username.toLowerCase().includes(this.search.toLowerCase()) ||
          user.email.toLowerCase().includes(this.search.toLowerCase()) ||
          user.role.toLowerCase().includes(this.search.toLowerCase())
        );
      });
    },
  },
  async mounted() {
    this.users = this.getUsers();
  },
}
</script>

<style scoped>

</style>
