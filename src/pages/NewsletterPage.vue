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
                color="blue-5"
                inverted
                class="text-white"
              />
            </div>
            <div class="col-auto">
              <q-btn
                icon="delete"
                inverted
                @click="deleteSelected"
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
  </div>
</template>

<script>
export default {
  name: "NewsletterPage",
  data() {
    return {
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
  methods: {
    async load() {
      this.loading = true;
      const response = await fetch("http://localhost:8000/newsletter", {
        method: "GET",
      });
      let newsletterMap = await response.json();
      this.rows = newsletterMap.map((newsletter) => {
        return {
          id: newsletter.id,
          email: newsletter.email,
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
        await fetch("http://localhost:8000/newsletter/" + emailsToDelete[i], {
          method: "DELETE",
        })
          .then((response) => {
            if (response.status === 200) {
              console.log("Email deleted");
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
