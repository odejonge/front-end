<template>
  <div class="container">
    <NavBar :user="user" />
    <div class="page-wrap">
      <router-view :userId="12345" :user="user"></router-view>
    </div> 
  </div>
</template>

<script>
import { getAuth, onAuthStateChanged} from 'firebase/auth';
import NavBar from '@/components/NavBar.vue'

export default {
  name: 'App',
  components: {
    NavBar,
  },
  data() {
    return {
      user: null
    }
  },
  created() {
    const auth = getAuth();
    onAuthStateChanged(auth, user => {
      console.log("auth changed, user: ", user)
      this.user = user
    })
  }
}
</script>

