<template>
  <div id="app">
    <UserLogin v-if="!isRegistering && !isLoggedIn" @login-success="handleLoginSuccess" @register="showRegistration" />
    <UserRegistration v-if="isRegistering" @registration-success="showLogin" @back-to-login="showLogin" />
    <div v-if="isLoggedIn">
      <nav>
        <button v-if="isUser" @click="currentView = 'Profile'">Profile</button>
        <button v-if="isUser" @click="currentView = 'MyBookings'">My Bookings</button>
        <button v-if="isUser" @click="currentView = 'MakeBooking'">Make Booking</button>
        <button v-if="isOwner" @click="currentView = 'MyCampingSpots'">My Camping Spots</button>
        <button v-if="isOwner" @click="currentView = 'AddCamping'">Add Camping</button>
        <button @click="logout">Logout</button>
      </nav>
      <component :is="currentView" :username="username"></component>
    </div>
  </div>
</template>

<script>
import UserLogin from './components/UserLogin.vue';
import UserRegistration from './components/UserRegistration.vue';
import Profile from './components/UserProfile.vue';
import MyBookings from './components/MyBookings.vue';  // Import MyBookings component
import MakeBooking from './components/MakeBooking.vue'; // Import MakeBooking component
import MyCampingSpots from './components/MyCampingSpots.vue'; // Import MyCampingSpots component
import AddCamping from './components/AddCamping.vue'; // Import AddCamping component

export default {
  name: 'App',
  components: {
    UserLogin,
    UserRegistration,
    Profile,
    MyBookings,
    MakeBooking,
    MyCampingSpots,
    AddCamping,
  },
  data() {
    return {
      isLoggedIn: !!localStorage.getItem('token'),
      isRegistering: false,
      username: localStorage.getItem('username') || '',
      isUser: window.location.pathname.includes('/user'),
      isOwner: window.location.pathname.includes('/owner'),
      currentView: window.location.pathname.includes('/owner') ? 'MyCampingSpots' : 'Profile', // Default view based on URL path
    };
  },
  methods: {
    handleLoginSuccess(username, userType) {
      this.isLoggedIn = true;
      this.username = username;
      localStorage.setItem('username', username);
      localStorage.setItem('userType', userType); // Store user type in localStorage

      // Set default view based on user type
      if (window.location.pathname.includes('/user')) {
        this.currentView = 'Profile';
      } else if (window.location.pathname.includes('/owner')) {
        this.currentView = 'MyCampingSpots';
      }
    },
    showRegistration() {
      this.isRegistering = true;
    },
    showLogin() {
      this.isRegistering = false;
    },
    logout() {
      this.isLoggedIn = false;
      localStorage.removeItem('token');
      localStorage.removeItem('username');
      localStorage.removeItem('userType');
      this.currentView = 'Profile'; // Default to Profile after logout
    },
  },
};
</script>

<style scoped>
/* Add any global styles here */
</style>
