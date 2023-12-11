<template>
  <div class="container">
    <NavBar />
    <div class="page-wrap">
      <router-view></router-view>
    </div>
  </div>
</template>
<script>
import NavBar from '@/components/NavBar.vue'
export default {
  name: 'App',
  components: {
    NavBar,
  }
}
</script>

