<template>
  <div class="page-my-account">
    <div class="columns is-multiline">
      <div class="column is-12">
        <h1 class="title">Захиалгын түүх</h1>
      </div>


      <hr />

      <div class="column is-12">
        <h2 class="subtitle">Захиалгын түүх</h2>
        <OrderSummary v-for="order in orders" v-bind:key="order.id" v-bind:order="order" />
      </div>
    </div>
    <div class="column is-12">
        <button @click="logoutHandler()" class="button is-danger">Гарах</button>
      </div>

  </div>
</template>

<script>
import axios from "axios";
import { logout } from "@/store/LogoutService";
import OrderSummary from "@/components/OrderSummary.vue";

export default {
  name: "MyAccount",
  components: {
    OrderSummary,
  },
  data() {
    return {
      orders: [],
    };
  },
  mounted() {
    document.title = "My account | Glee";

    this.getMyOrders();
  },
  methods: {
    logoutHandler() {
      logout.call(this); 
    },
    async getMyOrders() {
      this.$store.commit("setIsLoading", true);

      await axios
        .get("/api/v1/orders/")
        .then((response) => {
          this.orders = response.data;
          console.log(this.orders)
        })
        .catch((error) => {
          console.log(error);
        });

      this.$store.commit("setIsLoading", false);
    },
  },
};
</script>
