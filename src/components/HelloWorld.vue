<template>
  <div id="app">
    <h1>Chuck Norris Jokes by Category</h1>
    <div v-if="loading">Loading...</div>
    <div v-else class="container">
      <div class="card" v-for="(joke, index) in jokes" :key="index">
        <div class="emoji"></div>
        <div class="card-content">
          <h3 class="category">{{ joke.category }}</h3>
          <p class="date">{{ joke.date }}</p>
          <p class="description">{{ joke.description }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      jokes: [],
      loading: true,
    };
  },

  async mounted() {
    try {
      const categoriesResponse = await axios.get('https://api.chucknorris.io/jokes/categories');
      const categories = categoriesResponse.data; // Retrieve the categories from the response

      // Fetch jokes for each category
      const jokes = await Promise.all(
        categories.map(async (category) => {
          try {
            const jokeResponse = await axios.get(`https://api.chucknorris.io/jokes/search?query=${category}`);
            return {
              category: category.toUpperCase(),
              date: new Date().toLocaleString(),
              description: jokeResponse.data.result[0]?.value || 'No joke available',
            };
          } catch (error) {
            console.error(`Failed to fetch joke for ${category}:`, error);
            return {
              category: category.toUpperCase(),
              date: new Date().toLocaleString(),
              description: 'No joke available',
            };
          }
        })
      );

      this.jokes = jokes;
      this.loading = false;
    } catch (error) {
      console.error('Failed to fetch categories:', error);
      this.loading = false;
    }
  },
};
</script>

<style>

</style>
