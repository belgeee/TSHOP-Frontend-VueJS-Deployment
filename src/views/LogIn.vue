<template>
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Glee</title>
    <link rel="stylesheet" href="../assets/css/style.min.css" />
  </head>
  <div class="page-log-in">
    <div class="page-name">
      <div class="page-name__inner">
        <div class="container">
          <h2 class="title">Нэвтрэх</h2>
          <div class="breadcrumbs">
          </div>
        </div>
      </div>
    </div>
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <form class="modal__form" @submit.prevent="submitForm">
          <div class="field">
            <label>Хэрэглэгчийн нэр</label>
            <div class="control">
              <input type="text" class="input" id="username" v-model="username" />
            </div>
          </div>

          <div class="field">
            <label>Нууц үг</label>
            <div class="control">
              <input type="password" class="input" id="password" v-model="password" />
            </div>
          </div>

          <div class="notification is-danger" v-if="errors.length">
            <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
          </div>

          <div class="field">
            <div class="control">
              <button class="button is-dark" id="login">Нэвтрэх</button>
            </div>
          </div>

          <hr />

          Эсвэл <router-link to="/sign-up">энд дарж</router-link> бүртгүүлнэ үү!
        </form>
      </div>
    </div>
  </div>

  <main class="main">
    <section class="modal">
      <div class="container">
        <form class="modal__form" action="#">
          <label class="modal__label">
            Username or email address *
            <input class="modal__input" type="text" required />
          </label>
          <label class="modal__label">
            Password *
            <input class="modal__input" type="password" required />
          </label>
          <button class="modal__btn-login" type="submit">Login</button>
          <div class="modal__lost">
            <label>
              <input type="checkbox" />
              Remember me
            </label>
            <a class="modal__lost-password" href="#"> Lost your password? </a>
          </div>
        </form>
        <p class="modal__register-question" href="#">
          Not registered? No problem
        </p>
        <a class="modal__create-acclink" href="#"> Create an account </a>
      </div>
    </section>
  </main>
</template>

<script>
require("@/assets/css/style.min.css");
import axios from "axios";

export default {
  name: "LogIn",
  data() {
    return {
      username: "",
      password: "",
      errors: [],
    };
  },
  mounted() {
    document.title = "Log In ";
  },
  methods: {
    async submitForm() {
      axios.defaults.headers.common["Authorization"] = "";

      localStorage.removeItem("token");
      localStorage.removeItem("user");

      const formData = {
        username: this.username,
        password: this.password,
      };

      await axios
        .post("/api/v1/token/login/", formData)
        .then((response) => {
          const token = response.data.auth_token;

          this.$store.commit("setToken", token);
          this.$store.commit("setUser", this.username);
          axios.defaults.headers.common["Authorization"] = "Token " + token;
          localStorage.setItem("user", this.username);
          localStorage.setItem("token", token);

          const toPath = this.$route.query.to || "/";
          this.$router.push(toPath);
          setTimeout(() => {
            window.location.reload();
          }, 100);
        })
        .catch((error) => {
          if (error.response) {
            for (const property in error.response.data) {
              this.errors.push(`${property}: ${error.response.data[property]}`);
            }
          } else {
            this.errors.push("Something went wrong. Please try again");

            console.log(JSON.stringify(error));
          }
        });
    },
  },
};
</script>
