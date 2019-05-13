<template>
  <section class="container">
    <div>
      <div class="half"></div>
      <div class="half">
        <b-button v-b-modal.modalAdd variant="primary">
          + Add
        </b-button>
        <div class="search-cont">
          <input type="text" v-model="search" placeholder="Search" />
        </div>
        <!-- Modal Component -->
        <b-modal id="modalAdd" ref="modalAdd" title="Add Project" @ok="handleAdd">
          <b-form @submit.stop.prevent="submit">
            <b-form-group>
              <b-form-input type="text" v-model="project_code" placeholder="Project Code">
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-input type="text" v-model="project_name" placeholder="Project Name">
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-input type="text" v-model="client_id" placeholder="Client Id">
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-input type="text" v-model="purchase_order_id" placeholder="Purchase Order Id">
              </b-form-input>
            </b-form-group>
            <span class="notif">{{notif}}</span>
          </b-form>
        </b-modal>
      </div>
      <table border="0" class="person_tbl">
        <thead>
          <tr>
            <th @click="sort('project_code')" style="cursor:pointer">Project Code <img src="~/assets/sort.png" class="tbl-sort"></th>
            <th @click="sort('project_name')" style="cursor:pointer">Project Name <img src="~/assets/sort.png" class="tbl-sort"></th>
            <th @click="sort('client_id')" style="cursor:pointer">Client Customer Id <img src="~/assets/sort.png" class="tbl-sort"></th>
            <th @click="sort('purchase_order_id')" style="cursor:pointer">Purchase Order Id <img src="~/assets/sort.png" class="tbl-sort"></th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="project in filteredItems" v-bind:key="project">
            <td>{{ project['project_code'] }}</td>
            <td>{{ project['project_name'] }}</td>
            <td>{{ project['client_id'] }}</td>
            <td>{{ project['purchase_order_id'] }}</td>
            <td>
              <div>
                <b-button v-b-modal.modalEdit variant="primary" @click="showModal(project)">
                  Edit
                </b-button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
      <div class="pagination">
        <button @click="prevPage">&laquo;</button>
        <button @click="nextPage">&raquo;</button>
      </div>
      <!-- Modal Component -->
      <b-modal id="modalEdit" ref="modal" title="Edit Project" @ok="handleOk">
        <form @submit.stop.prevent="handleSubmit">
          <b-form-input type="text" placeholder="Project Code" v-model="projectModal.project_code"></b-form-input>
          <br>
          <b-form-input type="text" placeholder="Project Name" v-model="projectModal.project_name"></b-form-input>
          <br>
          <b-form-input type="text" placeholder="Client Id" v-model="projectModal.client_id"></b-form-input>
          <br>
          <b-form-input type="text" placeholder="Purchase Order Id" v-model="projectModal.purchase_order_id"></b-form-input>
        </form>
      </b-modal>
    </div>
  </section>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      date: [], //for the daterange
      currentSort: 'project_code',
      currentSortDir: 'asc',
      pageSize: 10,
      currentPage: 1,
      search: '',
      projects: [],
      projectModal: {},
      project_code: null,
      project_name: null,
      client_id: null,
      purchase_order_id: null,
      notif: '',
    };
  },
  head: {
    title: 'Projects List',
  },
  created: function() {
    this.onLoadData();
  },
  methods: {
    async submit() {
      try {
        let data = {
          project_code: this.project_code,
          project_name: this.project_name,
          client_id: this.client_id,
          purchase_order_id: this.purchase_order_id,
        };
        let response = await axios.post(
          'https://intempio-api-v3.herokuapp.com/api/v3/projects/',
          data
        );
        this.project_code = '';
        this.project_name = '';
        this.client_id = '';
        this.purchase_order_id = '';
        this.notif = 'Successfully Submitted!';
        // this.$router.push('/');
        this.$refs.modalAdd.hide();
        this.onLoadData();
        this.notif = '';
      } catch (e) {
        window.alert('Error logging in');
      }
    },
    async onLoadData() {
      try {
        let response = await axios.get(
          'https://intempio-api-v3.herokuapp.com/api/v3/projects/'
        );

        this.projects = response.data;
      } catch (e) {
        console.log('Error in function handleSubmit' + e);
      }
    },
    showModal(data) {
      this.projectModal = data;
    },
    clearName() {
      this.name = '';
    },
    handleOk(evt) {
      evt.preventDefault();
      this.handleSubmit();
      //}
    },
    handleAdd(evt) {
      evt.preventDefault();
      this.submit();
      //}
    },
    async handleSubmit() {
      try {
        let data = {
          project_code: this.projectModal.project_code,
          project_name: this.projectModal.project_name,
          client_id: this.projectModal.client_id,
          purchase_order_id: this.projectModal.purchase_order_id,
        };
        console.log('data' + data);
        let response = await axios.put(
          'https://intempio-api-v3.herokuapp.com/api/v3/projects/',
          data
        );
        console.log(response);
        this.$refs.modal.hide();
      } catch (e) {
        console.log('Error in function handleSubmit' + e);
      }
    },
    sort: function(s) {
      //if s == current sort, reverse
      if (s === this.currentSort) {
        this.currentSortDir = this.currentSortDir === 'asc' ? 'desc' : 'asc';
      }
      this.currentSort = s;
    },
    nextPage: function() {
      if (this.currentPage * this.pageSize < this.projects.length)
        this.currentPage++;
    },
    prevPage: function() {
      if (this.currentPage > 1) this.currentPage--;
    },
    sortedProjects: function() {
      return this.projects
        .sort((a, b) => {
          let modifier = 1;
          if (this.currentSortDir === 'desc') modifier = -1;
          if (a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
          if (a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
          return 0;
        })
        .filter((row, index) => {
          let start = (this.currentPage - 1) * this.pageSize;
          let end = this.currentPage * this.pageSize;
          if (index >= start && index < end) return true;
        });
    },
    searchProjects: function() {
      var self = this;
      return this.projects.filter(function(projects) {
        return (
          projects.project_name
            .toLowerCase()
            .indexOf(self.search.toLowerCase()) >= 0
        );
      });
    },
  },
  computed: {
    filteredItems: function() {
      let items = this.items;
      if (this.search.length > 0) {
        return this.searchProjects();
      }
      return this.sortedProjects();
    },
  },
};
</script>
