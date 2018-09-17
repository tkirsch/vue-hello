<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p v-if="hasErrors">
      <b>Please correct the following error(s):</b>
      <ul>
        <!-- <li v-for="error in errors" v-bind:key="error">{{ error }}</li> -->
        <Error v-for="error in errors" v-bind:key="error">{{ error }}</Error>
      </ul>
    </p>
    <form v-on:submit.prevent="login">
      <input type="text" v-model="user.username"><br>
      <input type="password" v-model="user.password"><br>
      <button type="submit" v-on:click="disableSubmit" v-bind:disabled="submitDisabled">OK</button>
    </form>
  </div>
</template>

<script>
import axios from "axios";

const API = axios.create({
  baseURL: "http://localhost:8080/api"
});

export default {
  name: "HelloWorld",
  components: {
    Error: {
      name: "Error",
      render(h) {
        return h("li", this.$slots.default);
      }
    }
  },
  props: {
    msg: String
  },
  data: function() {
    return {
      errors: [],
      submitDisabled: false,
      user: {
        username: null,
        password: null,
        rememberMe: false
      }
    };
  },
  computed: {
    hasErrors() {
      return this.errors.length;
    }
  },
  methods: {
    login() {
      this.errors = [];
      API.post("/authenticate", this.user)
        .then(response => {
          // JSON responses are automatically parsed.
          this.response = response.data;
          console.log(response.data);
        })
        .catch(e => {
          console.log(e);
          this.errors.push(e);
          this.submitDisabled = false;
        });
    },
    disableSubmit() {
      this.submitDisabled = true;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
