<template>
  <div class="container">
    <h1>Shopping Card</h1>
    <div v-if="cartItems.length > 0">
      <ShoppingCartList @remove-from-cart="removeProduct" :cartItems="cartItems" />
      <button  class="checkout-button">Proceed to checkout</button>
    </div>
    <div v-if="cartItems.length === 0">
      You currently have no items in your cart!
    </div>
  </div>
</template>

<script>
import ShoppingCartList from "@/components/ShoppingCartList.vue";
import axios from 'axios';

export default {
  name: "ShoppingCartPage",
  data() {
    return {
      cartItems: [],
    }
  },
  props: ['user','userId'],
  components: {
    ShoppingCartList
  },
  watch: {
    async user(newUser) {
      if(newUser) {
        const cartResponse = await axios.get(`/api/users/${newUser.uid}/cart`)
        this.cartItems = cartResponse.data
      }
    }
  },
  methods: {
    async removeProduct(productId) {
      const response = await axios.delete(`/api/users/${this.user.uid}/cart/${productId}`)
      const updatedCart = response.data
      this.cartItems = updatedCart
    }
  },
  async created() {
    if(this.user) {
      const response = await axios.get(`/api/users/${this.user.uid}/cart`)
      this.cartItems = response.data;
    }
  }
}
</script>