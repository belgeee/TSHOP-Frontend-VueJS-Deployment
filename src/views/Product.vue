<template>
  <div class="page-product">
    <div class="catalog-card2">
      <div class="catalog-card2__imgbox">
        <figure class="image mb-6">
          <img v-bind:src="product.get_thumbnail" />

        </figure>
      </div>
      <div class="catalog-card2__description">
        <h4 class="catalog-card2__title">
          {{ product.name }}
        </h4>
        <div class="catalog-card2__star" data-rateyo-rating="4"></div>
        <p class="catalog-card2__price">${{ product.price }}</p>
        <p class="catalog-card2__text">
          {{ product.description }}
        </p>
        <div class="field has-addons mt-6">
          <div class="control">
            <input type="number" class="input" min="1" v-model="quantity" />
          </div>

          <div class="control">
            <a class="button is-dark" @click="addToCart">Сагсанд нэмэх</a>
          </div>
        </div>
      </div>
    </div>
    <div class="columns is multiline">
      <div class="column is-9">
        <h2 class="title">Үнэлгээ:</h2>
        <div v-for="review in reviews" :key="review.id" class="review-wrapper">
          <p class="title">Үнэлгээний түүх</p>
          <p>Хэрэглэгч: Хэрэглэгч{{ review.user }}</p>
          <p>Үнэлгээ: {{ review.stars }}</p>
          <p class="review-wrapper__text">Сэтгэгдэл :{{ review.content }}</p>
          <p>Огноо: {{ review.date_added }}</p>
        </div>

        <div>
          <form  class="formreview" @submit.prevent="submitReview">
            <div>
            <label for="content">Сэтгэгдэл</label>
            <textarea v-model="review.content" id="content" required></textarea>
            </div>
            <div>
            <label for="stars">Од</label>
            <input
              v-model.number="review.stars"
              type="number"
              id="stars"
              required
              max="5"
            />
            </div>
            <button type="submit">Илгээх</button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.catalog-card2 {
  display: flex;
  justify-content: space-evenly;

  &__description {
    max-width: 700px;
  }
}
.columns.is-multiline{
  display: grid;
  grid-template-columns: 30% 30% 30%;
}
.review-wrapper {
  margin-bottom: 20px;
  border-bottom: 1px solid #a3bbc8;

  &__text {
    font-size: 16px;
    line-height: 30px;
    color: #2292a5;
  }
}

.review-card2__price{
  color:#2292a5;
}

/* Overall container for columns */
.columns {
  display: flex;
  flex-wrap: wrap;
}

/* Main column */
.column.is-9 {
  flex: 0 0 75%;
  max-width: 75%;
  padding: 20px;
  box-sizing: border-box;
}

/* Title styling */
.title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
}

/* Review wrapper styling */
.review-wrapper {
  border: 1px solid #ddd;
  padding: 15px;
  margin-bottom: 20px;
  border-radius: 8px;
  background-color: #f9f9f9;
}

/* Review text styling */
.review-wrapper__text {
  margin-top: 10px;
  font-size: 16px;
}

/* Form styling */
form {
  margin-top: 30px;
}

form label {
  display: block;
  font-size: 16px;
  margin-bottom: 5px;
}

form textarea,
form input[type="number"] {
  width: 40%;
  padding: 10px;
  margin-bottom: 15px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
  box-sizing: border-box;
}

form button {
  padding: 10px 15px;
  background-color: black;
  border: none;
  border-radius: 4px;
  color: #fff;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}


</style>

<script>
import axios from "axios";
import { toast } from "bulma-toast";

export default {
  name: "Product",
  data() {
    return {
      product: {},
      review: {},
      reviews: {},
      quantity: 1,
      user_id: null,
      user_info: {},
      userreviewid: [],
      review_userid_List: [],
    };
  },
  mounted() {
    this.getProduct(), this.getProductReviews(), this.getUserInfo();
  },
  methods: {
    async getProduct() {
      this.$store.commit("setIsLoading", true);

      const category_slug = this.$route.params.category_slug;
      const product_slug = this.$route.params.product_slug;

      await axios
        .get(`/api/v1/products/${category_slug}/${product_slug}`)
        .then((response) => {
          this.product = response.data;

          document.title = this.product.name + " | Glee";
        })
        .catch((error) => {
          console.log(error);
        });
      this.$store.commit("setIsLoading", false);
    },

    async getUserInfo() {
      await axios
        .get("api/v1/users/me/", {
          headers: {
            Authorization: `Token ${localStorage.getItem("token")}`,
          },
        })
        .then((response) => {
          this.user_info = response.data;
          this.user_id = response.data.id;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    async getProductReviews() {
      const category_slug = this.$route.params.category_slug;
      const product_slug = this.$route.params.product_slug;

      await axios
        .get(`/api/v1/products/${category_slug}/${product_slug}/reviews`)
        .then((response) => {
          this.reviews = response.data;
          this.review_userid_List = this.reviews.map((review) => review.user);
        })
        .catch((error) => {
          console.log(error);
        });
    },

    async submitReview() {
      if (this.review_userid_List.includes(this.user_id))
        // Checking whether the user has left a review for this product
        toast({
          message: "You already have a review for this product!",
          type: "is-warning",
          position: "center",
          duration: 3000,
        });
      else
        try {
          const category_slug = this.$route.params.category_slug;
          const product_slug = this.$route.params.product_slug;

          const reviewData = {
            user: this.user_id,
            content: this.review.content,
            stars: this.review.stars,
          };

          const response = await axios.post(
            `/api/v1/products/${category_slug}/${product_slug}/reviews/`,
            reviewData
          );
          console.log("Server response:", response.data);

          this.review = {};
          this.getProductReviews();

          toast({
            message: "Review sent successfully",
            type: "is-success",
            position: "bottom-right",
            duration: 3000,
          });
        } catch (error) {
          console.error("Error sending review:", error.response.data);

          toast({
            message: "Error sending review",
            type: "is-danger",
            position: "bottom-right",
            duration: 3000,
          });
        }
    },

    addToCart() {
      if (isNaN(this.quantity) || this.quantity < 1) {
        this.quantity = 1;
      }

      const item = {
        product: this.product,
        quantity: this.quantity,
      };

      this.$store.commit("addToCart", item);

      toast({
        message: "The product was added to the cart",
        type: "is-success",
        dismissible: true,
        pauseOnHover: true,
        duration: 2000,
        position: "bottom-right",
      });
    },
  },
};
</script>
