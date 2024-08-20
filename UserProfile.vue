<template>
  <div class="profile">
    <h2>Update Profile</h2>
    <form @submit.prevent="updateProfile">
      <div>
        <label for="username">Username:</label>
        <input type="text" id="username" v-model="user.username" required />
      </div>
      <div>
        <label for="email">Email:</label>
        <input type="email" id="email" v-model="user.email" required />
      </div>
      <div>
        <label for="fullname">Full Name:</label>
        <input type="text" id="fullname" v-model="user.fullName" required />
      </div>
      <div>
        <label for="password">Password:</label>
        <input type="password" id="password" v-model="user.password" placeholder="Leave blank to keep current password" />
      </div>
      <button type="submit">Save Changes</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'UserProfile',
  data() {
    return {
      user: {
        id: null,
        username: '',
        email: '',
        fullName: '',
        password: null,  // Leave empty/null if the user doesn't want to change it
        role: '',
        bookingIds: []
      },
    };
  },
  created() {
    // Fetch the current user data from the server or localStorage
    this.fetchUserData();
  },
  methods: {
    fetchUserData() {
      // Assuming you store the user's token in localStorage
      const token = localStorage.getItem('token');
      console.log("Token:", localStorage.getItem('token'));
      fetch('http://localhost:5010/User/me', { headers: { Authorization: `Bearer ${token}` } })
        .then(response => {
          if (!response.ok) throw new Error('Failed to fetch user data');
          return response.json();
        })
        .then(data => {
          if (data && data.id) {
            this.user = { 
              id: data.id,
              username: data.username, 
              email: data.email, 
              fullName: data.fullName, 
              password: null,  // We keep password null by default
              role: data.role,
              bookingIds: data.bookingIds || []
            };
          } else {
            throw new Error('User ID is null or undefined');
          }
        })
        .catch(error => {
          console.error('Error fetching user data:', error);
          alert('Error fetching user data. Please try again.');
        });
    },
    updateProfile() {
      const token = localStorage.getItem('token');
      
      // Only include the password if it has been changed
      const updatedUser = { ...this.user, password: this.user.password || null };

      fetch(`http://localhost:5010/User/update/${this.user.id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${token}`,
        },
        body: JSON.stringify(updatedUser),
      })
        .then(response => {
          if (!response.ok) throw new Error('Failed to update profile');
          return response.json();
        })
        .then(data => {
          alert('Profile updated successfully!');
          // Optionally update the user data in the component
          this.user = { ...data, password: null }; // Reset password after update
        })
        .catch(error => {
          console.error('Error updating profile:', error);
          alert('Error updating profile. Please try again.');
        });
    },
  },
};
</script>

<style scoped>
/* Add your styling here */
</style>
