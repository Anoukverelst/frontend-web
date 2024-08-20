<template>
    <div class="my-camping-spots">
      <h1>My Camping Spots</h1>
      <div v-if="loading" class="loading">
        Loading camping spots...
      </div>
      <div v-if="error" class="error">
        {{ error }}
      </div>
      <div v-if="campingSpots.length === 0 && !loading" class="no-spots">
        You have no camping spots.
      </div>
      <div v-if="campingSpots.length > 0" class="spots-list">
        <div v-for="spot in campingSpots" :key="spot.id" class="camping-spot">
          <h2>{{ spot.name }}</h2>
          <p><strong>Location:</strong> {{ spot.location }}</p>
          <p><strong>Description:</strong> {{ spot.description }}</p>
          <p><strong>Price Per Night:</strong> ${{ spot.pricePerNight }}</p>
          <p><strong>Max Capacity:</strong> {{ spot.maxCapacity }} people</p>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        campingSpots: [],
        loading: true,
        error: null,
      };
    },
    async created() {
      try {
        const response = await fetch('http://localhost:5010/CampingSpot/owner', {
          method: 'GET',
          headers: {
            'Content-Type': 'application/json',
          },
        });
  
        if (response.ok) {
          this.campingSpots = await response.json();
        } else if (response.status === 401) {
          this.error = 'You must be logged in to view your camping spots.';
        } else {
          this.error = 'Failed to load camping spots. Please try again.';
        }
      } catch (error) {
        this.error = 'An error occurred while fetching your camping spots.';
      } finally {
        this.loading = false;
      }
    },
  };
  </script>
  
  <style scoped>
  .my-camping-spots {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
  }
  .loading,
  .error,
  .no-spots {
    text-align: center;
    font-size: 1.2em;
    margin-top: 20px;
  }
  .spots-list {
    margin-top: 20px;
  }
  .camping-spot {
    padding: 15px;
    border: 1px solid #ccc;
    border-radius: 5px;
    margin-bottom: 15px;
    background-color: #f9f9f9;
  }
  .camping-spot h2 {
    margin-top: 0;
  }
  </style>
  