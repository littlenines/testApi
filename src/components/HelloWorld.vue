<template>
  <section>
    <div
      class="genre"
      v-for="(movieData, propertyName) of moviesByGenres"
      :key="propertyName"
    >
      <h2>{{ propertyName }}</h2>
      <div class="images">
        <div class="img" v-for="movie in movieData" :key="movie.id">
          <img :src="movie.img" :alt="movie.title" />
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import getGenres from "../../genres.json";
let genres = Object.values(getGenres).map((data) => data);
export default {
  data() {
    return {
      movies: [],
      moviesByGenres: {},
      titleGenre: [],
    };
  },
  methods: {
    async getMovies() {
      for (let i = 0; i < genres[0].length; i++) {
        this.titleGenre.push({ id: genres[0][i].id, name: genres[0][i].name });
        const response = await fetch(
          `https://api.themoviedb.org/3/discover/movie?with_genres=${genres[0][i].id}&api_key=d38aa8716411ef7d8e9054b34a6678ac`
        );
        const json = await response.json();
        const objectKey = Object.fromEntries(
          this.titleGenre.map((data) => [data.name, []])
        );
        this.moviesByGenres = objectKey;

        for (let j = 0; j < 5; j++) {
          this.movies.push({
            title: json.results[j].title,
            id: json.results[j].genre_ids,
            img:
              "https://image.tmdb.org/t/p/w1280" +
              json.results[j].backdrop_path,
            genre: genres[0][i].name,
          });
        }

        for (const key in objectKey) {
          for (let j = 0; j < this.movies.length; j++) {
            if (this.movies[j].genre === key) {
              objectKey[key].push(this.movies[j]);
            }
          }
        }
      }
    },
  },
  created() {
    this.getMovies();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
</style>
