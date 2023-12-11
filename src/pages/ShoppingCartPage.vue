<template>
  <div class="container">
    <h1>Shopping Card</h1>
    <div v-if="cartItems.length > 0">
      <ShoppingCartList :cartItems="cartItems" />
      <button  class="checkout-button">Proceed to checkout</button>
    </div>
    <div v-if="cartItems.length === 0">
      You currently have no items in your cart!
    </div>
  </div>
</template>

<script>
import {cartItems} from "../temp-data"
import ShoppingCartList from "@/components/ShoppingCartList.vue";

export default {
  name: "ShoppingCartPage",
  data() {
    return {
      cartItems,
    }
  },
  components: {
    ShoppingCartList
  }
}
</script>