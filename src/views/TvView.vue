<template>
  <h1>Séries</h1>
  <div class="ajuste">
    <select v-model="selectedGenre" @change="listTvs(selectedGenre)" class="genre-select">
      <option value="" disabled selected>Selecione uma categoria</option>
      <option v-for="genre in genreStore.genres" :key="genre.id" :value="genre.id">
          {{ genre.name }}
      </option>
  </select>
  </div>
  <loading v-model:active="isLoading" is-full-page />
  <div class="tv-list">
      <div v-for="tv in tvs" :key="tv.id" class="tv-card">
          <img :src="`https://image.tmdb.org/t/p/w500${tv.poster_path}`" :alt="tv.original_name" @click="openTv(tv.id)" />
          <div class="tv-details">
              <p class="tv-title">{{ tv.original_name }}</p>
              <p class="tv-release-date">{{ formatDate(tv.first_air_date) }}</p>
              <p class="tv-genres">
                  <span v-for="genre_id in tv.genre_ids" :key="genre_id" @click="listTvs(genre_id)"
                      :class="{ active: genre_id === genreStore.currentGenreId }">
                      {{ genreStore.getGenreName(genre_id) }}
                  </span>
              </p>
          </div>
      </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import api from '@/plugins/axios';
import Loading from 'vue-loading-overlay';
import { useGenreStore } from '@/stores/genre.js';
import { useRouter } from 'vue-router';

const tvs = ref([]);
const isLoading = ref(false);
const selectedGenre = ref(null);
const genreStore = useGenreStore();
const router = useRouter();

onMounted(async () => {
  isLoading.value = true;
  await genreStore.getAllGenres('tv');
  isLoading.value = false;
});

const listTvs = async (genreId) => {
  if (!genreId) return;
  genreStore.setCurrentGenreId(genreId);
  isLoading.value = true;
  const response = await api.get('discover/tv', {
      params: {
          with_genres: genreId,
          language: 'pt-BR',
      },
  });
  tvs.value = response.data.results;
  isLoading.value = false;
};

const formatDate = (date) => new Date(date).toLocaleDateString('pt-BR');

const openTv = (tvId) => {
  router.push({ name: 'TvDetails', params: { tvId } });
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Yuji+Mai&display=swap');
h1{
  text-align: center;
  font-size: 50px;
  font-family: 'Yuji Mai', cursive;
}
.ajuste{
  display: flex;
  justify-content: center;
}
.genre-select {
  font-family: 'Yuji Mai', cursive;
  width: 500px;
  margin-bottom: 2rem;
  padding: 0.5rem;
  border-radius: 0.5rem;
  border: 1px solid #ccc;
  background-color: #f9f9f9;
  font-size: 1rem;
}

.tv-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1rem;
  font-family: 'Yuji Mai', cursive;
  margin-bottom: 50px;
}

.tv-card {
  width: 15rem;
  height: 30rem;
  border-radius: 0.5rem;
  overflow: hidden;
  box-shadow: 0 0 0.5rem #000;
}

.tv-card img {
  width: 100%;
  height: 20rem;
  border-radius: 0.5rem;
  box-shadow: 0 0 0.5rem #000;
}

.tv-details {
  padding: 0 0.5rem;
}

.tv-title {
  font-size: 1.1rem;
  font-weight: bold;
  line-height: 1.3rem;
  height: 3.2rem;
}

.tv-release-date {
  font-size: 0.9rem;
  color: #666;
}
.tv-genres{
  display: flex;
  flex-wrap: wrap;
}
.tv-genres > span{
   border: 1px solid black;
   border-radius: 10px;
   background-color: #666;
   color: white;
}
</style>
