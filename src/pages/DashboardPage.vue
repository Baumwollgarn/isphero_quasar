<template>
<!--
      <q-table
        :rows="toDoList"
        :columns="columns"
        row-key="id"
        v-model:pagination="pagination"
        :rows-per-page-options="[0]"
        :loading="loading"
        :search="search"
        :sort="sort"
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
        <template v-slot:body-cell-task="props">
          <q-td :props="props">
            <q-item v-if="props.row.completed" class="input-group&#45;&#45;active">
              {{props.row.task}}
            </q-item>
            <q-item v-else>
              <q-item-section>
                <q-item-label>{{props.row.task}}</q-item-label>
              </q-item-section>
            </q-item>
          </q-td>
        </template>
        <template v-slot:body-cell-actions="props">
            <q-td :props="props">
              <q-item v-if="props.row.completed" class>
                  <q-btn dense flat color="grey" icon="X" @click="markAsCompleted(props.row.id)" />
              </q-item>
              <q-item v-else>
                  <q-btn dense flat color="green" icon="done" @click="markAsCompleted(props.row.id)" />
              </q-item>
            </q-td>
        </template>
      </q-table>
-->
</template>

<script>

import axios from "axios";

export default {
  name: "DashboardPage",
  data() {
    return {
      /*toDoList: [],
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
      }],
      sort: {
        field: 'id',
        direction: 'desc'
      }*/
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
    },
  },
  mounted() {
  }
}
</script>

<style scoped>
.input-group--active {
  text-decoration: line-through;
  color: grey;
}
</style>
