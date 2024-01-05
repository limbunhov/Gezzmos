<template>
    <div class="d-flex justify-content-center m-3 loading" v-if="loadingproduct" style=" height: 30rem;">
        <div class="spinner-grow text-primary" role="status">
            <span class="visually-hidden">Loading...</span>
        </div>
        <div class="spinner-grow text-success" role="status">
            <span class="visually-hidden">Loading...</span>
        </div>
    </div>
    <div v-if="user" class="bord_profile">
        <div class="profile_p">
            <img src="../assets/image/profile.png" alt="">
        </div>
        <div class="detail_pro">
            <h1>Username : <span>{{ user.fullName }}</span></h1>
            <h1>Email : <span>{{ user.email }}</span></h1>
            <h1>Role : <span>{{ user.role }}</span></h1>
        </div>
        <span @click="handleLogout" class="btn bg-danger m-1">Logout</span>
        <RouterLink to="/order-history" class="btn bg-primary">Order History</RouterLink>
    </div>
</template>
<script>
import axios from 'axios';
import { RouterLink } from 'vue-router';

export default {
    data() {
        return {
            user: null,
            loadingproduct: true,
            isLoggedInFromStorage: false,
            reload_logout: true
            // userlist: [
            //     { id: 1, name: 'salin', email: 'salin@gmail.com', contact: '093471111' }
            // ]
        };
    },
    components: { RouterLink },
    created() {
        this.fetchUser();
    },
    methods: {
        async fetchUser() {
            try {
                const userId = localStorage.getItem('userId');
                const response = await axios.get(`https://colorful-dog-teddy.cyclic.app/user/${userId}`);
                this.user = response.data.user;
                this.loadingproduct = false;
            } catch (error) {
                if (error.response && error.response.status === 404) {
                    console.error('User not found');
                } else {
                    console.error('Error fetching user data:', error);
                }
            }
        },

        async handleLogout() {
            try {
                await axios.post('https://colorful-dog-teddy.cyclic.app/logout');
                console.log('Logout successful');
                // Clear the token and user-related data from localStorage
                localStorage.removeItem('token');
                localStorage.removeItem('userId');
                // Redirect to the login page
                this.$router.push('/acount/login').then(() => {
                    // Reload the page after successful login
                    window.location.reload();
                });
                this.Account = false;
                this.isLoggedInFromStorage = false;
            } catch (error) {
                console.error('Logout failed', error.response.data);
                // Handle logout failure (optional)
            }
        },
    }
}

</script>