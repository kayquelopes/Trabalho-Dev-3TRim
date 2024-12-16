<template>
    <header>
        <nav>
            <router-link to="/">Home</router-link>
            <router-link to="/filmes">Filmes</router-link>
            <router-link to="/tv">Séries</router-link>
        </nav>
    </header>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Yuji+Mai&display=swap');
header {
    background-color: #000000;
    color: #ffffff;
    padding: 1rem;
    text-align: start;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
    font-family: 'Yuji Mai', cursive;
}

nav {
    display: flex;
    justify-content: start;
    column-gap: 2rem;
}

nav a {
    text-decoration: none;
    color: #ffffff;
    font-weight: bold;
    transition: color 0.3s;
}

nav a:hover {
    color: #5e5342;
}
</style>
