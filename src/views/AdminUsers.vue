<template>
  <div class="container py-5">
    <!-- AdminNav Component -->
    <AdminNav />
    <Spinner v-if="isLoading" />
    <table v-else class="table">
      <thead class="thead-dark">
        <tr>
          <th scope="col">#</th>
          <th scope="col">Email</th>
          <th scope="col">Role</th>
          <th scope="col" width="140">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id">
          <th scope="row">{{ user.id }}</th>
          <td>{{ user.email }}</td>
          <td>{{ user.isAdmin ? "admin" : "user" }}</td>
          <td>
            <button v-if=" currentUser.id !== user.id && currentUser.isAdmin " @click="toggleUserRole(user.id, user.isAdmin)" type="button" class="btn btn-link">{{ user.isAdmin ? "set as user" : "set as admin" }}</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import AdminNav from '../components/AdminNav.vue'
import Spinner from '../components/Spinner.vue'
import adminAPI from './../apis/admin'
import { Toast } from './../utils/helpers'
import { mapState } from 'vuex'
    
export default {
  created() {
    this.fetchUsers()
  },
  computed: {
    ...mapState(['currentUser'])
  },
  components: {
    AdminNav,
    Spinner
  },
  data() {
    return {
      users: [],
      isLoading: true
    }
  },
  methods: {
    async fetchUsers() {
      try {
        const { data } = await adminAPI.users.get()
        
        if(data.status == 'error') {
          throw new Error(data.message)
        }

        this.users = data.users
        this.isLoading = false
      } catch (error) {
        this.isLoading = false
        console.log('error', error);
        Toast.fire({
          icon: 'error',
          title: '無法取得使用者，請稍後再試'
        })
      }
    },
    async toggleUserRole(userId, isAdmin) {
      try {
        const { data } = await adminAPI.users.toggleUserRole({
          userId,
          isAdmin: (!isAdmin).toString()
        })

        if(data.status !== 'success') {
          throw new Error(data.message)
        }

        this.users = this.users.map(user => {
          if (user.id === userId) {
            return {
              ...user,
              isAdmin: !isAdmin
            }
          } else {
            return user
          }
        })
      } catch (error) {
        console.log('error', error);
        Toast.fire({
          icon: 'error',
          title: '無法更改權限，請稍後再試'
        })
      }
    }
  }
}
</script>