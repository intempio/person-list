<template>
  <section class="container">
    <div>
      <!--@reset="onReset"-->
      <b-form method="post" @submit.stop.prevent="submit()">
        <b-form-group>
          <b-form-input type="text" v-model="first_name" placeholder="First Name">
          </b-form-input>
        </b-form-group>
        <b-form-group>
          <b-form-input type="text" v-model="last_name" placeholder="Last Name">
          </b-form-input>
        </b-form-group>
        <b-form-group>
          <b-form-input type="email" v-model="email" placeholder="Email Address">
          </b-form-input>
        </b-form-group>
        <b-form-group>
          <b-form-input type="text" v-model="cell" placeholder="Cell">
          </b-form-input>
        </b-form-group>
        <b-form-group>
          <b-form-select :options="primary_comms" v-model="primary_comm" placeholder="Cell">
          </b-form-select>
        </b-form-group>
        <b-form-group>
          <b-form-textarea v-model="notes" placeholder="Enter Notes"></b-form-textarea>
        </b-form-group>
        <b-button type="submit" variant="primary">Submit</b-button>
        <p class="notif">{{notif}}</p>
      </b-form>
    </div>
  </section>
</template>
<script>
import axios from 'axios';
export default {
  data() {
    return {
      first_name: null,
      last_name: null,
      email: null,
      cell: null,
      primary_comm: null,
      notes: null,
      notif: '',
      primary_comms: [
        { text: 'Select Primary Comm Method', value: null },
        'Email',
        'Chat',
        'Call',
      ],
      show: true,
    };
  },
  title: 'Add People',
  methods: {
    async submit() {
      try {
        let data = {
          first_name: this.first_name,
          last_name: this.last_name,
          email: this.email,
          cell: this.cell,
          primary_comm_method: this.primary_comm,
          notes: this.notes,
        };
        let response = await axios.post(
          'https://intempio-api-v3.herokuapp.com/api/v3/persons/',
          data
        );
        this.first_name = '';
        this.last_name = '';
        this.email = '';
        this.cell = '';
        this.notes = '';
        this.notif = 'Successfully Submitted';
        this.$router.push('/');
        console.log('data sajdskf');
      } catch (e) {
        window.alert('Error logging in');
      }
    },
  },
};
</script>