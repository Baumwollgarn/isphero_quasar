<template>
  <!-- Markup table with E-mail addresses from an array. Be able to search and delete -->
  <div class="q-pa-md">
    <q-markup-table flat bordered>
      <thead class="bg-blue-7">
      <tr>
        <th colspan="5">
          <div class="row no-wrap items-center">
            <div class="col">
              <q-input
                v-model="search"
                debounce="500"
                rounded
                dense
                placeholder="Search"
                color="white"
                inverted
                class="text-white"
              />
            </div>
            <div class="col-auto">
              <q-btn
                icon="delete"
                color="red"
                inverted
                @click="confirm = true"
              />
            </div>
          </div>
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="row in rows" :key="row.id">
        <td style="display: none">{{ row.id }}</td>
        <td>{{ row.email }}</td>
        <td>
          <q-checkbox
            v-model="row.selected"
            dense
            color="red"
          />
        </td>
      </tr>
      </tbody>
    </q-markup-table>
    <q-dialog v-model="confirm" persistent>
      <q-card>
        <q-card-section class="row items-center">
          <q-avatar icon="delete" color="primary" text-color="white" />
          <span class="q-ml-sm">Are you sure you want to delete those emails?</span>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Cancel" color="primary" v-close-popup />
          <q-btn flat label="Delete" color="red" v-close-popup @click="deleteSelected"/>
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script>
import {ref} from "vue";
import { useQuasar } from "quasar";

export default {
  name: "NewsletterPage",
  data() {
    return {
      alert: ref(false),
      confirm: ref(false),
      prompt: ref(false),
      search: "",
      rows: [],
      loading: false,
      columns: [
        {
          name: "email",
          label: "E-mail",
          field: "email",
          align: "left",
          sortable: true,
        },
      ],
    };
  },
  setup() {
    const  q  = useQuasar();
    return {
      q,
      showNotificationDelete() {
        q.notify({
          message: "Emails deleted",
          color: "positive",
          position: "bottom",
          timeout: 10000,
          icon: 'announcement',
          actions: [{ icon: "close", color: "white", label: "Close", handler: () => {
              console.log("Close notification");
            }
          }],
        });
      }
    };
  },
  methods: {
    async load() {
      this.loading = true;
      const response = await fetch("http://isphero.com:1234/newsletter", {
        method: "GET",
      });
      let newsletterMap = await response.json();
      this.rows = newsletterMap.map((newsletter) => {
        return {
          id: newsletter.id,
          email: newsletter.email,
          selected: false,
        };
      });
      this.loading = false;
    },
    deleteSelected() {
      var emailsToDelete = [];
      this.rows = this.rows.filter((row) => {
        if (row.selected) {
          emailsToDelete.push(row.id);
        }
        return !row.selected;
      });
      this.deleteEmails(emailsToDelete);
    },
    async deleteEmails(emailsToDelete) {
      for (let i = 0; i < emailsToDelete.length; i++) {
        await fetch("http://isphero.com:1234/newsletter/" + emailsToDelete[i], {
          method: "DELETE",
        })
          .then((response) => {
            if (response.status === 200) {
              console.log("Email deleted");
              this.showNotificationDelete();
            }
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
  },
  mounted() {
    this.load();
  }
}
</script>

<style scoped>

</style>
