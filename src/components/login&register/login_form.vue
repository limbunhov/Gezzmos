<template>
  <div class="main-login">
    <div class="container" id="login">
      <div class="cart_form">
        <h2>Login Form</h2>
        <form @submit.prevent="handleLogin">
          <label>Email address:</label>
          <i class="fa-regular fa-envelope"></i>
          <input type="email" id="loginEmail" v-model="loginData.email" placeholder="Email..." required
            @input="clearError">

          <label for="loginPassword">Password:</label>
          <i class="fa-solid fa-lock"></i>
          <input type="password" id="loginPassword" v-model="loginData.password" placeholder="Password..." required
            @input="clearError">

          <div class="z-1 d-flex flex-column justify-content-center align-items-center">
            <button type="submit" class="btn btn-primary">Login</button>
            <span>Not a member? <a @click="toggleForm">Register</a></span>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      isLoggedIn: true,
      loginData: {
        email: '',
        password: '',
      },
      fetchedUserData: null,
      loginError: null, // Added property to track login error
    };
  },
  methods: {
    handleLogin() {
      axios.post('https://colorful-dog-teddy.cyclic.app/login', this.loginData)
        .then(response => {
          // console.log('Login successful', response.data);
          // Successful login logic
          this.loginError = null; // Clear login error on success
          Swal.fire({
            icon: 'success',
            title: 'Done',
            text: 'Login is success!',
          });
          localStorage.setItem('token', response.data.token);
          localStorage.setItem('userId', response.data.userId);
          localStorage.setItem('userRole', response.data.userRole);
          this.$router.push('/').then(() => {
            window.location.reload();
          });
        })
        .catch(error => {
          // console.error('Login failed', error.response.data);

          // Set login error and display error message
          this.loginError = 'Incorrect email or password. Please try again.';
          Swal.fire({
            icon: 'error',
            title: 'Fail',
            text: this.loginError,
          });
          this.loginData = {
            email: '',
            password: '',
          }
        });
    },

    clearError() {
      // Clear login error when user interacts with input fields
      this.loginError = null;
    },

    toggleForm() {
      this.isRegister = !this.isRegister;
      this.$router.push('/acount/register');
    },
  },
};
</script>

<style scoped>
/* Your scoped styles */
</style>
