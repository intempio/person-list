<template>
  <section class="container">
    <div>
      <div class="half"></div>
      <div class="half">
        <a href="/create">
          <button class="add-btn">+ Add</button>
        </a>
        <div class="search-cont">
          <input type="text" v-model="search" placeholder="Search" />
        </div>
      </div>
      <table border="0" class="person_tbl">
        <thead>
          <tr>
            <th @click="sort('first_name')" style="cursor:pointer">First Name <img src="~/assets/sort.png" class="tbl-sort"></th>
            <th @click="sort('last_name')" style="cursor:pointer">Last Name <img src="~/assets/sort.png" class="tbl-sort"></th>
            <th @click="sort('email')" style="cursor:pointer">Email <img src="~/assets/sort.png" class="tbl-sort"></th>
            <th>Cell </th>
            <th>Primary <br/> Comm Method</th>
            <th>Notes</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="person in filteredItems" v-bind:key="person">
            <td>{{ person['first_name'] }}</td>
            <td>{{ person['last_name'] }}</td>
            <td>{{ person['email'] }}</td>
            <td>{{ person['cell'] }}</td>
            <td>{{ person['primary_comm_method'] }}</td>
            <td>{{ person['notes'] }}</td>
            <td>
              <a href="/create">
                <button>Edit</button>
              </a>
            </td>
          </tr>
        </tbody>
      </table>
      <div class="pagination">
        <button @click="prevPage">&laquo; Previous</button>
        <button @click="nextPage">Next &raquo;</button>
      </div>
    </div>
  </section>
</template>

<script>
import axios from 'axios';
export default {
  // components: { DatePicker },
  data() {
    return {
      date: [], //for the daterange
      currentSort: '',
      currentSortDir: 'asc',
      pageSize: 10,
      currentPage: 1,
      search: '',
      persons: [],
    };
  },
  head: {
    title: 'People List',
  },
  created: function() {
    this.onLoadData();
  },
  methods: {
    async onLoadData() {
      try {
        let response = await axios.get(
          'https://intempio-api-v3.herokuapp.com/api/v3/persons/'
        );

        this.persons = response.data;
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
      if (this.currentPage * this.pageSize < this.persons.length)
        this.currentPage++;
    },
    prevPage: function() {
      if (this.currentPage > 1) this.currentPage--;
    },
    sortedPersons: function() {
      return this.persons.sort((a, b) => {
        let modifier = 1;
        if (this.currentSortDir === 'desc') modifier = -1;
        if (a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
        if (a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
        return 0;
      });
      /* .filter((row, index) => {
          let start = (this.currentPage - 1) * this.pageSize;
          let end = this.currentPage * this.pageSize;
          if (index >= start && index < end) return true;
        });*/
    },
    searchPersons: function() {
      var self = this;
      return this.persons.filter(function(persons) {
        return (
          persons.last_name.toLowerCase().indexOf(self.search.toLowerCase()) >=
          0
        );
      });
    },
  },
  computed: {
    filteredItems: function() {
      let items = this.items;
      items = this.sortedPersons(items);
      items = this.searchPersons(items);
      return items;
    },
  },
};
</script>
