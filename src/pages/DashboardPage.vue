<template>
      <q-table
        :rows="toDoList"
        :columns="columns"
        row-key="id"
        v-model:pagination="pagination"
        :rows-per-page-options="[0]"
        :loading="loading"
        :search="search"
        >
        <template v-slot:top>
          <q-toolbar>
            <q-toolbar-title>Tasks</q-toolbar-title>
            <q-space />
            <q-input
              v-model="search"
              dense
              rounded
              debounce="500"
              placeholder="Search"
            >
              <template v-slot:before>
                <q-icon name="search" />
              </template>
            </q-input>
          </q-toolbar>
        </template>
        <template v-slot:body-cell-actions="props">
          <q-tr :props="props">
            <q-td>
              <q-item >
                {{props.row.completed}}
              </q-item>
              <q-item>
                <q-item-section>
                  {{ props.value }}
                </q-item-section>
              </q-item>
            </q-td>
          </q-tr>
        </template>
      </q-table>
</template>

<script>
export default {
  name: "DashboardPage",
  data() {
    return {
      toDoList: [],
      loading: false,
      search: '',
      pagination: {
        sortBy: 'id',
        descending: false,
        page: 1,
        rowsPerPage: 5,
      },
      columns: [{
        name: 'id',
        label: 'ID',
        field: 'id',
        align: 'left',
        sortable: true,
      },
        {
          name: 'task',
          label: 'Task',
          field: 'task',
          align: 'left',
          sortable: true,
        },
        {
          name: 'actions',
          label: 'Actions',
          field: 'actions',
          align: 'left',
      }]
    }
  },
  methods:{
    getToDoList(){
      this.loading = true
      this.$axios.get(process.env.API + '/todo')
        .then(response => {
          this.toDoList = response.data
          this.loading = false
          console.log(response.data)
        })
        .catch(error => {
          console.log(error)
        })
    },
    markAsCompleted(id){
      this.$axios.put(process.env.API + '/todo/' + id)
        .then(response => {
          this.getToDoList()
        })
        .catch(error => {
          console.log(error)
        })
    }
  },
  mounted() {
    this.getToDoList()
  }
}
</script>

<style scoped>

</style>
