<template>
  <div class="container py-5">
    <Spinner v-if="isLoading" />
    <template v-else>
      <div>
        <h1>{{ restaurant.name }}</h1>
        <span class="badge badge-secondary mt-1 mb-3">
          {{ restaurant.categoryName }}
        </span>
      </div>

      <hr />

      <ul>
        <li>評論數： {{ restaurant.commentsLength }}</li>
        <li>瀏覽次數： {{ restaurant.viewCounts }}</li>
      </ul>

      <button type="button" class="btn btn-link" @click="$router.back()">
        回上一頁
      </button>
    </template>
  </div>
</template>

<script>
import Spinner from "../components/Spinner.vue";
import restaurantsAPI from "./../apis/restaurants";
import { Toast } from "./../utils/helpers";

export default {
  components: {
    Spinner,
  },
  data() {
    return {
      restaurant: {
        id: -1,
        name: "",
        categoryName: "",
        viewCounts: -1,
        commentsLength: -1,
      },
      isLoading: true,
    };
  },
  methods: {
    async fetchDashboard(restaurantId) {
      try {
        const { data } = await restaurantsAPI.getRestaurant({ restaurantId });

        this.restaurant = {
          id: data.restaurant.id,
          name: data.restaurant.name,
          categoryName: data.restaurant.Category.name,
          viewCounts: data.restaurant.viewCounts,
          commentsLength: data.restaurant.Comments.length,
        };

        this.isLoading = false;
      } catch (error) {
        this.isLoading = false;
        console.log("error", error);
        Toast.fire({
          icon: "error",
          title: "無法取得餐廳dashboard資訊，請稍後再試",
        });
      }
    },
  },
  created() {
    const { id: restaurantId } = this.$route.params;
    this.fetchDashboard(restaurantId);
  },
  beforeRouteUpdate(to, from, next) {
    const { id: restaurantId } = to.params;
    this.fetchDashboard(restaurantId);
    next();
  },
};
</script>
