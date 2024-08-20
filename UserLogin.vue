<template>
  <div id="user-login">
    <h2>Login</h2>
    <form @submit.prevent="login">
      <div>
        <label for="username">Username:</label>
        <input type="text" v-model="username" required />
      </div>
      <div>
        <label for="password">Password:</label>
        <input type="password" v-model="password" required />
      </div>
      <button type="submit">Login</button>
    </form>
    <p v-if="error">{{ error }}</p>
    <button @click="$emit('register')">Register</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'UserLogin',
  data() {
    return {
      username: '',
      password: '',
      error: null,
    };
  },
  methods: {
    async login() {
      try {
        // Attempt to log in as a user
        const response = await axios.post('http://localhost:5010/User/login', {
          username: this.username,
          password: this.password,
        });

        // If successful, proceed as a user
        if (response.status === 200) {
          const token = response.data.token || 'placeholder-token';
          const role = response.data.role || 'User';

          localStorage.setItem('token', token);
          localStorage.setItem('role', role);
          window.location.href = '/user'; // Redirect to the User page
        }
      } catch (err) {
        if (err.response && err.response.status === 401) {
          try {
            // If unauthorized as a user, attempt to log in as an owner
            const ownerResponse = await axios.post('http://localhost:5010/Owner/login', {
              username: this.username,
              password: this.password,
            });

            if (ownerResponse.status === 200) {
              const ownerToken = ownerResponse.data.token || 'placeholder-owner-token';
              const role = 'Owner';

              localStorage.setItem('token', ownerToken);
              localStorage.setItem('role', role);
              window.location.href = '/owner'; // Redirect to the Owner page
            } else {
              this.error = 'Invalid username or password';
            }
          } catch (ownerErr) {
            this.error = 'Invalid username or password';
          }
        } else {
          this.error = 'Invalid username or password';
        }
      }
    },
  },
};
</script>



<style scoped>
#user-login {
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
