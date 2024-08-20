<template>
  <div class="add-camping">
    <h1>Add New Camping Spot</h1>
    <form @submit.prevent="submitForm">
      <div>
        <label for="name">Name:</label>
        <input type="text" v-model="campingSpot.name" id="name" required />
      </div>
      
      <div>
        <label for="location">Location:</label>
        <input type="text" v-model="campingSpot.location" id="location" required />
      </div>

      <div>
        <label for="description">Description:</label>
        <textarea v-model="campingSpot.description" id="description" required></textarea>
      </div>

      <div>
        <label for="pricePerNight">Price Per Night:</label>
        <input type="number" v-model="campingSpot.pricePerNight" id="pricePerNight" required />
      </div>

      <div>
        <label for="maxCapacity">Max Capacity:</label>
        <input type="number" v-model="campingSpot.maxCapacity" id="maxCapacity" required />
      </div>

      <button type="submit">Add Camping Spot</button>
    </form>

    <p v-if="message">{{ message }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      campingSpot: {
        name: '',
        location: '',
        description: '',
        pricePerNight: 0,
        maxCapacity: 0,
        ownerID: null, // Add ownerID to the data object
      },
      message: '',
    };
  },
  methods: {
    async submitForm() {
      try {
        // Retrieve the ownerID from the session (assuming you have a way to access it)
        const ownerID = this.getOwnerIDFromSession();
        
        // Set the ownerID in the campingSpot object
        this.campingSpot.ownerID = ownerID;

        const response = await fetch('http://localhost:5010/CampingSpot', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(this.campingSpot),
        });
        
        if (response.ok) {
          this.message = 'Camping spot added successfully!';
          this.clearForm();
        } else if (response.status === 401) {
          this.message = 'You must be logged in as an owner to add a camping spot.';
        } else {
          this.message = 'Failed to add camping spot. Please try again.';
        }
      } catch (error) {
        this.message = 'An error occurred while adding the camping spot.';
      }
    },
    clearForm() {
      this.campingSpot = {
        name: '',
        location: '',
        description: '',
        pricePerNight: 0,
        maxCapacity: 0,
        ownerID: null,
      };
    },
    getOwnerIDFromSession() {
      // Placeholder function to simulate retrieving ownerID from the session
      // Replace this with your actual logic to get the ownerID
      return '12345'; // Example ownerID
    },
  },
};
</script>

<style scoped>
.add-camping {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
}
form div {
  margin-bottom: 15px;
}
label {
  display: block;
  margin-bottom: 5px;
}
input, textarea {
  width: 100%;
  padding: 8px;
  box-sizing: border-box;
}
button {
  padding: 10px 15px;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
button:hover {
  background-color: #218838;
}
</style>
