<template>
  <Spinner v-if="isLoading" />
  <div v-else class="container py-5">
    <div class="row">
      <div class="col-md-12">
        <h1>{{ restaurant.name }}</h1>
        <span class="badge badge-secondary mt-1 mb-3">
          {{ restaurant.categoryName }}
        </span>
      </div>
      <div class="col-md-4">
        <img
          class="img-responsive center-block"
          :src="restaurant.image | emptyImage"
          style="width: 250px;margin-bottom: 25px;"
        >
        <div class="well">
          <ul class="list-unstyled">
            <li>
              <strong>Opening Hour:</strong>
              {{ restaurant.openingHours }}
            </li>
            <li>
              <strong>Tel:</strong>
              {{ restaurant.tel }}
            </li>
            <li>
              <strong>Address:</strong>
              {{ restaurant.address }}
            </li>
          </ul>
        </div>
      </div>
      <div class="col-md-8">
        <p>{{ restaurant.description }}</p>
      </div>
    </div>
    <hr>
    <button
      type="button"
      class="btn btn-link"
      @click="$router.back()"
    >回上一頁</button>
  </div>
</template>

<script>
import adminAPI from './../apis/admin'
import Spinner from '../components/Spinner.vue'
import { Toast } from '../utils/helpers'
import { emptyImageFilter } from './../utils/mixins'


export default {
  name: 'AdminRestaurant',
  mixins: [emptyImageFilter],
  components: {
    Spinner
  },
  data () {
    return {
      restaurant: {
        id: -1,
        name: '',
        categoryName: '',
        image: '',
        openingHours: '',
        tel: '',
        address: '',
        description: ''
      },
      isLoading: true
    }
  },
  mounted () {
    const { id: restaurantId } = this.$route.params
    this.fetchRestaurant(restaurantId)
  },
  methods: {
    async fetchRestaurant (restaurantId) {
      try {
        const { data } = await adminAPI.restaurants.getDetail({ restaurantId })
        const { restaurant } = data
        this.restaurant = {
          ...this.restaurant,
          id: restaurant.id,
          name: restaurant.name,
          categoryName: restaurant.Category.name,
          image: restaurant.image,
          openingHours: restaurant.opening_hours,
          tel: restaurant.tel,
          address: restaurant.address,
          description: restaurant.description
        }
        this.isLoading = false
      } catch (error) {
        this.isLoading = false
        Toast.fire({
          icon: 'error',
          title: '無法取得餐廳資料，請稍後再試'
        })
        console.log('error', error)
      }
    }
  },
  beforeRouteUpdate(to, from, next) {
    const { id } = to.params
    this.fetchRestaurant(id)
    next()
  }
}
</script>