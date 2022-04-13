<template>
  <div class="container-fluid mt-5">
    <div class="row">
      <div class="col-md-4">
        <div class="card">
          <div class="card-body">
            MENU UTAMA
            <hr>
            <ul class="list-group">
              <router-link :to="{name: 'dashboard'}"
                class="list-group-item text-dark text-decoration-none">DASHBOARD</router-link>
              <li @click.prevent="logout" class="list-group-item text-dark text-decoration-none"
                style="cursor:pointer">LOGOUT</li>
            </ul>
          </div>
        </div>
      </div>
      <div class="col-md-8">
        <div class="card">
          <div class="card-body">
            DASHBOARD
            <hr>
            Welcome! <strong>{{ user.name }}</strong>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { onMounted, ref } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

export default {
  setup() {    

    //state token
    const token = localStorage.getItem('token')

    //inisialisasi vue router on Composition API
    const router = useRouter()

    //state user
    const user = ref('')

    //mounted property
    onMounted(() =>{

      //check token exist
      if(!token) {
        return router.push({
          name: 'login'
        })
      }

      //get data user
      axios.defaults.headers.common.Authorization = `Bearer ${token}`
      axios.get('http://localhost:8000/api/user')
      .then(response => {

        //console.log(response.data.name)
        user.value = response.data
      })
      .catch(error => {
        console.log(error.response.data)
      })
    })

    //method logout
    function logout() {
      //logout
      axios.defaults.headers.common.Authorization = `Bearer ${token}`
      axios.post('http://localhost:8000/api/logout')
      .then(response => {

        if(response.data.success) {

          //remove localStorage
          localStorage.removeItem('token')

          //redirect kembali ke halaman login
          return router.push({
            name: 'login'
          })
        }
      })
      .catch(error => {
        console.log(error.response.data)
      })
    }

    return {
      token,   // <-- state token
      user,    // <-- state user
      logout   // <-- method logout
    }
  },
}
</script>