<template>
    <h1>Filmes</h1>
    <select v-model="selectedGenre" @change="listMovies(selectedGenre)" class="genre-select">
        <option value="" disabled selected>Selecione uma categoria</option>
        <option v-for="genre in genreStore.genres" :key="genre.id" :value="genre.id">
            {{ genre.name }}
        </option>
    </select>
    <loading v-model:active="isLoading" is-full-page />
    <div class="movie-list">
        <div v-for="movie in movies" :key="movie.id" class="movie-card">
            <img :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`" :alt="movie.title"
                @click="openMovie(movie.id)" />
            <div class="movie-details">
                <p class="movie-title">{{ movie.title }}</p>
                <p class="movie-release-date">{{ formatDate(movie.release_date) }}</p>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import api from '@/plugins/axios';
import { useGenreStore } from '@/stores/genre.js';
import { useRouter } from 'vue-router';

const genres = ref([]);
const movies = ref([]);
const isLoading = ref(false);
const selectedGenre = ref(null);
const genreStore = useGenreStore();
const router = useRouter();

onMounted(async () => {
    isLoading.value = true;
    await genreStore.getAllGenres('movie');
    genres.value = genreStore.genres;
    isLoading.value = false;
});

const listMovies = async (genreId) => {
    if (!genreId) return;
    genreStore.setCurrentGenreId(genreId);
    isLoading.value = true;
    const response = await api.get('discover/movie', {
        params: {
            with_genres: genreId,
            language: 'pt-BR',
        },
    });
    movies.value = response.data.results;
    isLoading.value = false;
};

const formatDate = (date) => new Date(date).toLocaleDateString('pt-BR');
const openMovie = (movieId) => router.push({ name: 'MovieDetails', params: { movieId } });
</script>

<style scoped>
h1{
  text-align: center;
}
.genre-select {
    margin-bottom: 2rem;
    padding: 0.5rem;
    border-radius: 0.5rem;
    border: 1px solid #ccc;
    background-color: #f9f9f9;
    font-size: 1rem;
}

.movie-list {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
}

.movie-card {
    width: 15rem;
    height: 30rem;
    border-radius: 0.5rem;
    overflow: hidden;
    box-shadow: 0 0 0.5rem #000;
    display: flex;
    flex-direction: column;
}

.movie-card img {
    width: 100%;
    height: 20rem;
    border-radius: 0.5rem;
    box-shadow: 0 0 0.5rem #000;
}

.movie-details {
    padding: 0 0.5rem;
}

.movie-title {
    font-size: 1.1rem;
    font-weight: bold;
    line-height: 1.3rem;
    height: 3.2rem;
}

.movie-release-date {
    font-size: 0.9rem;
    color: #666;
}
</style>
