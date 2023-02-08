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
              <template v-slot:after>
                <q-btn
                  icon="add"
                  color="green"
                  @click="editUser('new')"
                />
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
    <q-tr v-for="user in usersFiltered" :key="user.id" class="text-center">
      <q-td>{{ user.id }}</q-td>
      <q-td>{{ user.first_name }}</q-td>
      <q-td>{{ user.last_name1 }}</q-td>
      <q-td>{{ user.username }}</q-td>
      <q-td>{{ user.email }}</q-td>
      <q-td>{{ user.role }}</q-td>
      <q-td>
        <q-btn color="primary" icon="edit" @click="editUser(user.id)" />
        <q-btn color="negative" icon="delete" @click="confirm = true; setDeleteId(user.id)" />
        <q-btn color="black" icon="vpn_key" @click="showServices(user.id)" />
      </q-td>
    </q-tr>
  </q-markup-table>
  <q-dialog v-model="confirm" persistent>
    <q-card>
      <q-card-section class="row items-center">
        <q-avatar icon="delete" color="primary" text-color="white" />
        <span class="q-ml-sm">Are you sure you want to delete this user?</span>
      </q-card-section>

      <q-card-actions align="right">
        <q-btn flat label="Cancel" color="primary" v-close-popup />
        <q-btn flat label="Delete" color="red" v-close-popup @click="deleteUser"/>
      </q-card-actions>
    </q-card>
  </q-dialog>
  </div>
</template>

<script>
import {ref} from "vue";
import {useQuasar} from "quasar";

export default {
  name: "UserPage",
  data() {
    return {
      alert: ref(false),
      confirm: ref(false),
      prompt: ref(false),
      search: "",
      users: [],
      usersFiltered: [],
      deleteId: null,
      responseMessage: null,
      colorMessage: null,
    };
  },
  methods: {
    setDeleteId(id) {
      this.deleteId = id;
    },
    async getUsers() {
      const response = await fetch(process.env.API + "/users", {
        method: "GET",
      })
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
      return this.users;
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
    deleteUser() {
      fetch(`${process.env.API}/user/${this.deleteId}`, {
        method: "DELETE",
      })
        .then(async (response) => {
          if (response.status === 200) {
            this.responseMessage = "User deleted successfully";
            this.colorMessage = "positive";
            this.showNotificationDelete()
            this.usersFiltered = await this.getUsers();
          }
          if (response.status === 400) {
            response.json().then((data) => {
              this.responseMessage = data.message;
              this.colorMessage = "negative";
              this.showNotificationDelete()
            });
          }
        })
        .catch((error) => {
          console.error("Error:", error);
        });

    },
    editUser(id) {
      this.$router.push(`/home/edit-user/${id}`)
    },
  },
  setup() {
    const  q  = useQuasar();
    return {
      showNotificationDelete() {
        q.notify({
          message: this.responseMessage,
          color: this.colorMessage,
          position: "bottom",
          timeout: 10000,
          icon: 'person',
          actions: [{ icon: "close", color: "white", label: "Close", handler: () => {
              console.log("Close notification");
            }
          }],
        });
      },
    }
  },
  async mounted() {
    this.usersFiltered = await this.getUsers();
  },
}
</script>

<style scoped>
</style>
