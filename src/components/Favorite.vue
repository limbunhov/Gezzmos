<template>
  <section>
    <div class="name_page">
      <h1>Favorite product</h1>
    </div>
    <div class="d-flex justify-content-center loading" v-if="loadingproduct">
      <div class="spinner-grow text-primary" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
      <div class="spinner-grow text-success" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
    <div v-if="isFavorite && !loading" class="fav_board">
      <div class="fav_cart" v-for="favorite in favorites" :key="favorite._id">
        <div class="fav_image">
          <img :src="favorite.product.image" :alt="favorite.product.title.t" />
        </div>
        <div class="fav_detail">
          <div style="width: 40rem;">
            <h2>{{ favorite.product.name }}</h2>
            <p>{{ favorite.product.title.t1 }}</p>
            <p>{{ favorite.product.title.t2 }}</p>
            <p>{{ favorite.product.title.t3 }}</p>
            <p>{{ favorite.product.title.t4 }}</p>
            <!-- Add other product details here -->
          </div>
          <span>${{ favorite.product.price }}</span>
          <div style="display: flex; flex-direction: column; width: 5rem;">
            <div class="price_pro">
              <div class="price_pro">
               <span @click="addToCart(favorite.product)"><i class="fa-solid fa-cart-shopping"></i></span>
              </div>
            </div>
            <button @click="removeFromFavorites(favorite.product._id)" type="button" class="btn bg-danger button">
              Remove
            </button>
          </div>
        </div>
      </div>
    </div>
    <div v-if="!isFavorite && !loading" class="not_add_cart m-3">
      <p>Sorry!! Please add to favorites.</p>
    </div>
  </section>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      favorites: [],
      isFavorite: true,
      loading: false,
      loadingproduct: true

    };
  },
  created() {
    this.fetchFavorites();
  },
  methods: {
    async fetchFavorites() {
      try {
        const userId = localStorage.getItem('userId');
        console.log(userId);
        const response = await axios.get(`https://colorful-dog-teddy.cyclic.app/favorites/${userId}`);
        this.favorites = response.data;
        this.isFavorite = this.favorites.length > 0;
        this.loadingproduct = this.favorite
        console.error(response.data);
      } catch (error) {
        console.error('Error fetching favorites:', error);
      }
    },
    async removeFromFavorites(productId) {
      try {
        const token = localStorage.getItem('token');
        const userId = localStorage.getItem('userId');

        if (!token || !userId) {
          console.error('Token or userId not found.');
          return;
        }

        console.log(userId, productId);

        await axios.delete(`https://colorful-dog-teddy.cyclic.app/favorites/${userId}/${productId}`);

        // Send a request to your server to remove the product from favorites
        // const response = await axios.delete(`http://localhost:3000/remove/favorites`, {
        //     data: {
        //         userId: userId,
        //         productId: productId,
        //     },
        // });


        this.favorites = this.favorites.filter(product => product.product._id !== productId);
        // console.error('Failed to remove from favorites:', response.data.error);
      } catch (error) {
        console.error('Error removing from favorites:', error);
      }
    },
    async addToCart(product) {
      const token = localStorage.getItem('token');
      const userId = localStorage.getItem('userId');
      // console.log('Token:', token);
      // console.log('UserId:', userId);
      if (!token || !userId) {
        // User is not logged in, prompt them to log in or redirect to the login page
        Swal.fire({
          icon: "error",
          title: "Faill",
          text: "please login or register !!",
        });
        // Redirect to the login page
        this.$router.push('/acount/login');
        return;
      }
      const quantity = 1; // Define the quantity variable here or adjust as needed

      try {
        // Check if the product is defined before accessing its properties
        if (product && product._id) {
          // Make a POST request to add the product to the user's cart
          const response = await axios.post('https://colorful-dog-teddy.cyclic.app/add-to-cart', {
            userId: userId,
            productId: product._id,
            quantity: quantity, // Use the defined quantity variable
          });
          Swal.fire({
            icon: "success",
            title: "Done",
            text: "Add to cart is successfully!!",
          });
          console.log('Added to cart:', response.data);
          // Optionally, you can update the local cart state or take other actions
        } else {
          console.error('Invalid product:', product);
        }
      } catch (error) {
        console.error('Error adding to cart:', error);
      }
    },


    // Fetch cart items when the component is created
    async fetchCartItems() {
      const userId = 'your-user-id'; // Replace with the actual user ID

      try {
        const response = await axios.get(`https://colorful-dog-teddy.cyclic.app/cart/${userId}`);
        this.cart = response.data;
      } catch (error) {
        console.error('Error fetching cart items:', error);
      }
    },


  },
};
</script>
