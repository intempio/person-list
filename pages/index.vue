<template>
  <section class="container">
    <div>
      <div class="half"></div>
      <div class="half">
        <b-button
          v-b-modal.modalAdd
          variant="primary"
        >
          + Add
        </b-button>
        <div class="search-cont">
          <input
            type="text"
            v-model="search"
            placeholder="Search"
          />
        </div>
        <!-- Modal Component -->
        <b-modal
          id="modalAdd"
          ref="modalAdd"
          title="Add Person"
          @ok="handleAdd"
        >
          <b-form @submit.stop.prevent="submit">
            <b-form-group>
              <b-form-input
                type="text"
                v-model="first_name"
                placeholder="First Name"
              >
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-input
                type="text"
                v-model="last_name"
                placeholder="Last Name"
              >
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-input
                type="email"
                v-model="email"
                placeholder="Email Address"
              >
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-input
                type="text"
                v-model="cell"
                placeholder="Phone Number"
              >
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-select
                :options="primary_comms"
                v-model="primary_comm"
                placeholder="Primary Communication"
              >
              </b-form-select>
            </b-form-group>
            <b-form-group>
              <b-form-textarea
                v-model="notes"
                placeholder="Enter Notes"
              ></b-form-textarea>
            </b-form-group>
            <!--<b-button type="submit" variant="primary">Submit</b-button>-->
            <span class="notif">{{notif}}</span>
          </b-form>
        </b-modal>
      </div>
      <table
        border="0"
        class="person_tbl"
      >
        <thead>
          <tr>
            <th
              @click="sort('first_name')"
              style="cursor:pointer"
            >First Name <img
                src="~/assets/sort.png"
                class="tbl-sort"
              ></th>
            <th
              @click="sort('last_name')"
              style="cursor:pointer"
            >Last Name <img
                src="~/assets/sort.png"
                class="tbl-sort"
              ></th>
            <th
              @click="sort('email')"
              style="cursor:pointer"
            >Email <img
                src="~/assets/sort.png"
                class="tbl-sort"
              ></th>
            <th>Cell </th>
            <th>Primary <br /> Comm Method</th>
            <th>Notes</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="person in filteredItems"
            v-bind:key="person"
          >
            <td>{{ person['first_name'] }}</td>
            <td>{{ person['last_name'] }}</td>
            <td>{{ person['email'] }}</td>
            <td>{{ person['cell'] }}</td>
            <td>{{ person['primary_comm_method'] }}</td>
            <td>{{ person['notes'] }}</td>
            <td class="id">{{ person['person_id'] }}</td>
            <td>
              <div>
                <b-button
                  v-b-modal.modalEdit
                  variant="primary"
                  @click="showModal(person)"
                >
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
      <b-modal
        id="modalEdit"
        ref="modal"
        title="Edit Person"
        @ok="handleOk"
      >
        <form @submit.stop.prevent="handleSubmit">
          <b-form-input
            type="text"
            placeholder="First Name"
            v-model="personModal.first_name"
          ></b-form-input>
          <br>
          <b-form-input
            type="text"
            placeholder="Last Name"
            v-model="personModal.last_name"
          ></b-form-input>
          <br>
          <b-form-input
            type="text"
            placeholder="Email"
            v-model="personModal.email"
          ></b-form-input>
          <br>
          <b-form-input
            type="text"
            placeholder="Cell"
            v-model="personModal.cell"
          ></b-form-input>
          <br>
          <b-form-input
            type="text"
            placeholder="Primary Comm Method"
            v-model="personModal.primary_comm_method"
            :options="primary_comms"
          ></b-form-input>
          <br>
          <b-form-textarea
            placeholder="Notes"
            v-model="personModal.notes"
          ></b-form-textarea>
          <br>
        </form>
      </b-modal>
    </div>
  </section>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      date: [], //for the daterange
      currentSort: "first_name",
      currentSortDir: "asc",
      pageSize: 10,
      currentPage: 1,
      search: "",
      persons: [],
      personModal: {},
      first_name: null,
      last_name: null,
      email: null,
      cell: null,
      primary_comm: null,
      notes: null,
      notif: "",
      primary_comms: [
        { text: "Select Primary Comm Method", value: null },
        "Email",
        "Chat",
        "Call"
      ],
      show: true
    };
  },
  head: {
    title: "People List"
  },
  created: function() {
    this.onLoadData();
  },
  methods: {
    async submit() {
      try {
        let data = {
          first_name: this.first_name,
          last_name: this.last_name,
          email: this.email,
          cell: this.cell,
          primary_comm_method: this.primary_comm,
          notes: this.notes
        };
        let response = await axios.post(
          "https://intempio-api-v3.herokuapp.com/api/v3/persons/",
          data
        );
        this.first_name = "";
        this.last_name = "";
        this.email = "";
        this.cell = "";
        this.notes = "";
        this.notif = "Successfully Submitted!";
        // this.$router.push('/');
        this.$refs.modalAdd.hide();
        this.onLoadData();
        this.notif = "";
      } catch (e) {
        window.alert("Error logging in");
      }
    },
    async onLoadData() {
      try {
        let response = await axios.get(
          "https://intempio-api-v3.herokuapp.com/api/v3/persons/"
        );

        this.persons = response.data;
      } catch (e) {
        console.log("Error in function handleSubmit" + e);
      }
    },
    showModal(data) {
      this.personModal = data;
    },
    clearName() {
      this.name = "";
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
          person_id: this.personModal.person_id,
          first_name: this.personModal.first_name,
          last_name: this.personModal.last_name,
          email: this.personModal.email,
          primary_comm_method: this.personModal.primary_comm_method,
          cell: this.personModal.cell,
          notes: this.personModal.notes
        };
        console.log("data" + data);
        let response = await axios.put(
          "https://intempio-api-v3.herokuapp.com/api/v3/persons/",
          data
        );
        console.log(response);
        this.$refs.modal.hide();
      } catch (e) {
        console.log("Error in function handleSubmit" + e);
      }
    },
    sort: function(s) {
      //if s == current sort, reverse
      if (s === this.currentSort) {
        this.currentSortDir = this.currentSortDir === "asc" ? "desc" : "asc";
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
      /*  if (this.search.length <= 0) {
        return this.searchPersons();
      }*/
      return this.persons
        .sort((a, b) => {
          let modifier = 1;
          if (this.currentSortDir === "desc") modifier = -1;
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
    searchPersons: function() {
      var self = this;
      return this.persons.filter(function(persons) {
        return (
          persons.last_name.toLowerCase().indexOf(self.search.toLowerCase()) >=
          0
        );
      });
    }
  },
  computed: {
    filteredItems: function() {
      let items = this.items;
      if (this.search.length > 0) {
        return this.searchPersons();
      }
      return this.sortedPersons();
    }
  }
};
</script>
