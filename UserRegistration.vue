<template>
  <div id="user-registration">
    <h2>Register</h2>
    <form @submit.prevent="register">
      <div>
        <label for="username">Username:</label>
        <input type="text" id="username" v-model="newUser.username" required />
      </div>
      <div>
        <label for="email">Email:</label>
        <input type="email" id="email" v-model="newUser.email" required />
      </div>
      <div>
        <label for="fullName">Full Name:</label>
        <input type="text" id="fullName" v-model="newUser.fullName" required />
      </div>
      <div>
        <label for="password">Password:</label>
        <input type="password" id="password" v-model="newUser.password" required />
      </div>
      <div>
        <label>Role:</label>
        <input type="radio" id="user" value="User" v-model="newUser.role" required />
        <label for="user">User</label>
        <input type="radio" id="owner" value="Owner" v-model="newUser.role" required />
        <label for="owner">Owner</label>
      </div>
      <button type="submit">Register</button>
    </form>
    <p v-if="error">{{ error }}</p>
    <button @click="$emit('back-to-login')">Back to Login</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'UserRegistration',
  data() {
    return {
      error: null,
      newUser: {
        username: '',
        email: '',
        fullName: '',
        password: '',
        role: '',
        bookingIds: [] 
      },
    };
  },
  methods: {
    async register() {
      if (!this.validateRegistration()) return;

      try {
        let apiUrl = 'http://localhost:5010/User/register';

        if (this.newUser.role === 'Owner') {
          apiUrl = 'http://localhost:5010/Owner/register';
        }

        const response = await axios.post(apiUrl, this.newUser);

        if (response.status === 201) {
          this.$emit('registration-success');
          this.resetRegistrationForm();
        }
      } catch (err) {
        if (err.response && err.response.data) {
          this.error = err.response.data.message || 'Registration failed';
        } else {
          this.error = 'Registration failed';
        }
      }
    },
    validateRegistration() {
      this.error = null;

      if (!this.newUser.username || !this.newUser.email || !this.newUser.fullName || !this.newUser.password || !this.newUser.role) {
        this.error = 'All fields are required.';
        return false;
      }

      return true;
    },
    resetRegistrationForm() {
      this.newUser = {
        username: '',
        email: '',
        fullName: '',
        password: '',
        role: '',
        bookingIds: [],
      };
      this.error = null;
    },
  },
};
</script>

<style scoped>
#user-registration {
  max-width: 400px;
  margin: 50px auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

button {
  width: 100%;
  padding: 10px;
}

label {
  display: block;
  margin-bottom: 5px;
}

input {
  width: 100%;
  padding: 8px;
  margin-bottom: 10px;
  box-sizing: border-box;
}

p {
  color: red;
}
</style>
