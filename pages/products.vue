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
          title="Add product"
          @ok="handleAdd"
        >
          <b-form @submit.stop.prevent="submit">
            <b-form-group>
              <b-form-input
                type="text"
                v-model="product_description"
                placeholder="product Code"
              >
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-input
                type="text"
                v-model="product_description"
                placeholder="product Name"
              >
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-input
                type="text"
                v-model="client_id"
                placeholder="Client Id"
              >
              </b-form-input>
            </b-form-group>
            <b-form-group>
              <b-form-input
                type="text"
                v-model="duration_minutes"
                placeholder="Purchase Order Id"
              >
              </b-form-input>
            </b-form-group>
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
            <th>Prod ID</th>
            <th
              @click="sort('product_name')"
              style="cursor:pointer"
            >Product Name <img
                src="~/assets/sort.png"
                class="tbl-sort"
              ></th>
            <th
              @click="sort('product_description')"
              style="cursor:pointer"
            >Product Description <img
                src="~/assets/sort.png"
                class="tbl-sort"
              ></th>
            <th colspan="2">Actions
              <ul class="sub-heading">
                <li>Edit Actions</li>
                <li>List Actions</li>
              </ul>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr></tr>
          <tr
            v-for="product in filteredItems"
            v-bind:key="product"
          >
            <td>{{product['product_id'] }}</td>
            <td class="prod_name"><img src="~/assets/edit.png">{{ product['product_name'] }}</td>
            <td>{{ product['product_description'] }}</td>
            <td>
              <b-button
                v-b-modal.modalEdit
                variant="primary"
                @click="showModal(product)"
              >
                Client
              </b-button>
              <b-button
                v-b-modal.modalEdit
                variant="primary"
                @click="showModal(product)"
              >
                Product
              </b-button>
              <b-button
                v-b-modal.modalEdit
                variant="primary"
                @click="showModal(product)"
              >
                Checklist
              </b-button>
            </td>
            <td>
              <div>
                <b-button
                  v-b-modal.modalEdit
                  variant="primary"
                  @click="showModal(product)"
                >
                  Clone
                </b-button>
                <b-button @click="actionDelete">
                  Delete
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
        title="Edit Product"
        @ok="handleOk"
      >
        <table>
          <tr>
            <th></th>
            <th>Items</th>
            <th>Content</th>
            <th>Action</th>
          </tr>
          <tr>
            <td><img src="/_nuxt/assets/edit.png"></td>
            <td>
              <b-form-input
                type="text"
                placeholder="Item1"
              ></b-form-input>
            </td>
            <td>
              <b-form-input
                type="text"
                placeholder="Content"
              ></b-form-input>
            </td>
            <td><button @click="nextPage">+ ADD</button></td>
          </tr>
        </table>
        <div class="pagination">
          <button @click="prevPage">&laquo;</button>
          <button @click="nextPage">&raquo;</button>
        </div>
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
      currentSort: "product_name",
      currentSortDir: "asc",
      pageSize: 10,
      currentPage: 1,
      search: "",
      products: [],
      productModal: {},
      product_name: null,
      product_description: null,
      client_id: null,
      duration_minutes: null,
      notif: ""
    };
  },
  head: {
    title: "products List"
  },
  created: function() {
    this.onLoadData();
  },
  methods: {
    async actionDelete() {
      try {
        let data;
      } catch (e) {
        window.alert("Error logging in");
      }
    },
    async submit() {
      try {
        let data = {
          product_name: this.product_name,
          product_description: this.product_description,
          client_id: this.client_id,
          duration_minutes: this.duration_minutes
        };
        let response = await axios.post(
          "https://intempio-api-v3.herokuapp.com/api/v3/products/",
          data
        );
        this.product_name = "";
        this.product_description = "";
        this.client_id = "";
        this.duration_minutes = "";
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
          "https://intempio-api-v3.herokuapp.com/api/v3/products/"
        );

        this.products = response.data;
      } catch (e) {
        console.log("Error in function handleSubmit" + e);
      }
    },
    showModal(data) {
      this.productModal = data;
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
          product_name: this.productModal.product_name,
          product_description: this.productModal.product_description,
          client_id: this.productModal.client_id,
          duration_minutes: this.productModal.duration_minutes
        };
        console.log("data" + data);
        let response = await axios.put(
          "https://intempio-api-v3.herokuapp.com/api/v3/products/",
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
      if (this.currentPage * this.pageSize < this.products.length)
        this.currentPage++;
    },
    prevPage: function() {
      if (this.currentPage > 1) this.currentPage--;
    },
    sortedproducts: function() {
      return this.products
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
    searchproducts: function() {
      var self = this;
      return this.products.filter(function(products) {
        return (
          products.product_description
            .toLowerCase()
            .indexOf(self.search.toLowerCase()) >= 0
        );
      });
    }
  },
  computed: {
    filteredItems: function() {
      let items = this.items;
      if (this.search.length > 0) {
        return this.searchproducts();
      }
      return this.sortedproducts();
    }
  }
};
</script>
