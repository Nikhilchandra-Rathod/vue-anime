<template>
  <div class="app">
    <header>
      <h1>The<strong>Anime</strong>Database</h1>

      <form class="search-box" @submit.prevent="handleSearch">
        <input
          type="text"
          class="search-field"
          placeholder="Search for an anime..."
          required
          v-model="searchQuery" />
      </form>
    </header>

    <main>
      <div v-if="isLoading">
        <h3>Please wait...</h3>
      </div>
      <div v-else>
        <div class="cards" v-if="animeList.length > 0">
          <Card
            v-for="anime in animeList"
            :key="anime.mal_id"
            :anime="anime" />
        </div>
        <div class="no-results" v-else>
          <h3>No result</h3>
        </div>
      </div>
    </main>

  </div>
</template>

<script>
  import { ref } from 'vue'
  import Card from './components/Card.vue';
  export default {
    components: {
      Card
    },
    setup() {
      const searchQuery = ref('')
      const isLoading = ref(false)
      const animeList = ref([])

      const handleSearch = async () => {
        isLoading.value = true;
        try {
          var result = await fetch(`https://api.jikan.moe/v3/search/anime?q=${searchQuery.value}`)
          var list = await result.json();

          animeList.value = list.results;
          searchQuery.value = '';
          isLoading.value = false;
        } catch (error) {
          animeList.value = [];
          searchQuery.value = '';
          isLoading.value = false;
        }
      }

      return {
        searchQuery,
        animeList,
        handleSearch,
        isLoading
      }
    }
  }
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html,
input,
button {
  font-family: 'Inter', sans-serif;
}

@supports (font-variation-settings: normal) {
  html { font-family: 'Inter var', sans-serif; }
}

a {
  text-decoration: none;
}

header {
  padding-top: 50px;
  padding-bottom: 50px;
}

header h1 {
  color: #888;
  font-size: 42px;
  font-weight: 400;
  text-align: center;
  text-transform: uppercase;
  margin-bottom: 30px;
  transition: 0.4s;
}

header strong {
  color: #313131;
}

header h1:hover {
  color: #313131;
}

header .search-box {
  display: flex;
  justify-content: center;
  padding-left: 30px;
  padding-right: 30px;
}

header .search-box .search-field {
  appearance: none;
  background: none;
  border: none;
  outline: none;
  background-color: #F3F3F3;
  box-shadow: 0px 4px 8px rgba(0,0,0,0.15);

  display: block;
  width: 100%;
  max-width: 600px;
  padding: 15px;
  border-radius: 8px;
  color: #313131;
  font-size: 20px;
  transition: 0.4s;
}

header .search-box .search-field::placeholder {
  color: #AAAAAA;
}

header .search-box .search-field:focus,
header .search-box .search-field:valid {
  color: #FFF;
  background-color: #313131;
  box-shadow: 0 0 0 rgba(0,0,0,0.15);
}

main {
  max-width: 1200px;
  margin: 0 auto;
  padding-left: 30px;
  padding-right: 30px;
}

main .cards {
  display: flex;
  flex-wrap: wrap;
  margin: 0 -8px;
}
</style>