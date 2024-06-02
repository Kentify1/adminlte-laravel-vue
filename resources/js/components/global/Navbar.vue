<template>
    <!-- Navbar -->
    <nav class="main-header navbar navbar-expand navbar-white navbar-light">
        <!-- Left navbar links -->
        <ul class="navbar-nav">
            <li class="nav-item">
                <a class="nav-link" data-widget="pushmenu" href="#" role="button"><i class="fas fa-bars"></i></a>
            </li>
        </ul>
    
        <!-- Right navbar links -->
        <ul class="navbar-nav ml-auto">
            <li class="nav-item dropdown" ref="dropdown">
                <a class="nav-link dropdown-toggle" href="#" role="button" @click="toggleDropdown">
                    <img :src="photo" class="img-circle elevation-2" alt="User Image" style="width: 30px; height: 30px;">
                    <span class="d-none d-md-inline text-capitalize">{{ username }}</span>
                </a>
                <div class="dropdown-menu dropdown-menu-right" :class="{ show: isOpen }" aria-labelledby="userDropdown">
                    <!-- User profile or additional options here -->
                    <a class="dropdown-item" href="#">Profile</a>
                    <div class="dropdown-divider"></div>
                    <a class="dropdown-item" href="#" @click="logout">Logout</a>
                </div>
            </li>
        </ul>
    </nav>
    <!-- /.navbar -->
</template>

<script>
import axios from 'axios'
import Errors from '../partials/Errors.vue'

export default {
    components: { Errors },
    name: "Navbar",
    data() {
        return {
            username: '', // Dynamically set the username
            photo: '', // Store the user's photo URL
            isOpen: false, // Track dropdown menu state
            disable: false
        }
    },
    mounted() {
        // Fetch the user info when the component is mounted
        this.fetchUserInfo();
    },
    methods: {
        toggleDropdown() {
            this.isOpen = !this.isOpen; // Toggle dropdown menu
        },
        logout() {
            // Disable the logout button
            this.disable = true
            // Get the CSRF token
            let token = document.head.querySelector('meta[name="csrf-token"]').getAttribute('content')
            // Log the user out
            axios.post('/logout', null, {
                headers: {
                    'X-CSRF-TOKEN': token
                }
            })
            .then(() => {
                console.log('logged out successfully')
                window.location.href = '/'
            })
            .catch(() => /* Enable the logout button */ this.disable = false)
        },
        fetchUserInfo() {
            axios.get('/api/user', {
                headers: {
                    'Authorization': `Bearer ${localStorage.getItem('api_token')}`,
                    'Accept': 'application/json',
                    'X-Requested-With': 'XMLHttpRequest',
                    'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]').getAttribute('content')
                }
            })
            .then(response => {
                this.username = response.data.username; // Assuming the server returns the user's name as 'name'
                this.photo = response.data.photo; // Assuming the server returns the user's photo URL as 'photo'
            })
            .catch(error => {
                console.error('Error fetching user info:', error);
                if (error.response) {
                    console.log(error.response.data);
                    console.log(error.response.status);
                    console.log(error.response.headers);
                } else if (error.request) {
                    console.log(error.request);
                } else {
                    console.log('Error', error.message);
                }
            });
        }
    }
}
</script>


<style>
    .logo {
        height: 45px;
        width: 250px;
    }
    .logo-mobile {
        height: 60px;
        width: 200px;
    }
    #form {
        background: white;
        padding: 25px;
        border-radius: 14px;
        border: 2px solid #14756a23;
    }
</style>
