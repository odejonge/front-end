<template>
  <div class="container">
    <div v-if="product">
      <div class="img-wrap">
        <img :src="product.imageUrl" />
      </div>
      <div class="product-details">
        <h1>{{ product.name }}</h1>
        <h3 class="price">{{ product.price }}</h3>
        <button @click="addToCart" class="add-to-cart" v-if="user && !productOnCart(cartItems)">Add to cart</button>
        <button class="grey-button" v-if="user && productOnCart(cartItems)">Item is already in cart</button>
        <button class="sign-in" @click="signIn" v-if="!user">Sign in to add to cart</button>
      </div>
    </div>
    <div v-if="!product">
      <NotFound/>
    </div>
  </div>
</template>

<script>
import {getAuth, sendSignInLinkToEmail, isSignInWithEmailLink, signInWithEmailLink} from 'firebase/auth';
import NotFound from '@/components/NotFound.vue'
import axios from 'axios'

export default {
  name: "ProductDetailPage",
  data() {
    return {
      product: {},
      cartItems: [],
    }
  },
  props: ['user', 'userId'],
  components: {
    NotFound,
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
    productOnCart(cartItems) {
      return cartItems.find(item => item.id == this.product.id)!=null;
    },
    async addToCart() {
      await axios.post(`/api/users/${this.user.uid}/cart`, 
      {
        id:this.$route.params.productId
      })
      alert('Successfully added item to cart!');
      const response = await axios.get(`/api/users/${this.user.uid}/cart`)
      this.cartItems = response.data
    },
    async signIn() {
      const email = prompt('Please enter your emailto sign in:');
      const auth = getAuth();
      const baseUrl = `${window.location.protocol}//${window.location.host}`;
      const actionCodeSettings = {
        url: `${baseUrl}/products/${this.$route.params.productId}`,
        handleCodeInApp: true
      } 
      await sendSignInLinkToEmail(auth, email, actionCodeSettings);
      alert('A login link was sent to the email you provided');
      window.localStorage.setItem('emailForSignIn', email);
    }
  },
  async created(){
    const auth = getAuth();
    console.log({auth})
    if(isSignInWithEmailLink(auth, window.location.href)) {
      let email = window.localStorage.getItem('emailForSignIn')
      if (!email) {
        email = window.prompt('Please provide your email for confirmation');
      }
      try{
        await signInWithEmailLink(auth, email, window.location.href);
        alert('successfully signed in!');
        window.localStorage.removeItem('emailForSignIn');
      }catch(error) {
        console.error(error)
      }
    }

    const response = await axios.get(`/api/products/${this.$route.params.productId}`)
    this.product = response.data

    if (this.user) {
      const cartResponse = await axios.get(`/api/users/${this.user.uid}/cart`)
      this.cartItems = cartResponse.data
    }
  },
}
</script>