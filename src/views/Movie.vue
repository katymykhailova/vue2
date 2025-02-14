<template>
  <section class="container">
    <movie-details v-if="movie" :movie="movie">
      <template #trailerBtn="{ movie = {} }">
        <button-trailer :movie="movie" @click="trailer(movie.id)">
          <svg
            class="trailer-svg"
            version="1.1"
            xmlns="http://www.w3.org/2000/svg"
            width="32"
            height="32"
            viewBox="0 0 32 32"
          >
            <path
              d="M30.662 5.003c-4.488-0.645-9.448-1.003-14.662-1.003s-10.174 0.358-14.662 1.003c-0.86 3.366-1.338 7.086-1.338 10.997s0.477 7.63 1.338 10.997c4.489 0.645 9.448 1.003 14.662 1.003s10.174-0.358 14.662-1.003c0.86-3.366 1.338-7.086 1.338-10.997s-0.477-7.63-1.338-10.997zM12 22v-12l10 6-10 6z"
            ></path>
          </svg>
        </button-trailer>
      </template>
    </movie-details>
  </section>
  <section class="container">
    <template v-if="reqStatus === 'resolved'">
      <router-link :to="{ name: 'Cast' }" class="movie-details-link"
        >Cast
      </router-link>
      <router-link :to="{ name: 'Reviews' }" class="movie-details-link"
        >Reviews
      </router-link>
      <a @click.stop="onWatchedClick" class="movie-details-link">
        {{ addedMovie ? 'Remove from watched' : 'Add to watched' }}
      </a>
    </template>
  </section>
  <router-view :addMessage="addMessage"></router-view>
  <teleport v-if="movie" to="#modal">
    <modal :title="movie.original_title" ref="trailerModal">
      <iframe
        width="560"
        height="315"
        :src="'https://www.youtube.com/embed/' + idMoviesTrailer"
        frameborder="0"
        allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
        allowfullscreen
      ></iframe>
    </modal>
  </teleport>
</template>

<script>
import MovieDetails from '../components/MovieDetails.vue';
import ButtonTrailer from '../components/ButtonTrailer.vue';
import Modal from '../components/Modal.vue';
import { fetchMovieDetails, fetchTrailerMovie } from '../services/apiService';

export default {
  name: 'Movie',
  components: {
    MovieDetails,
    ButtonTrailer,
    Modal,
  },

  data() {
    return {
      movie: null,
      toWatchArray: [],
      addedMovie: false,
      reqStatus: 'idle',
      idMoviesTrailer: null,
    };
  },

  props: {
    addMessage: { type: Function, required: true },
  },

  created: async function () {
    const movieId = this.$route.params.movieId;
    if (!movieId) {
      return;
    }
    try {
      this.reqStatus = 'pending';
      this.movie = await fetchMovieDetails(movieId);
      this.reqStatus = 'resolved';
      this.toWatchArray = JSON.parse(localStorage.getItem('WATCHED')) || [];
      this.addedMovie = this.toWatchArray.some(
        ({ id }) => id === this.movie.id,
      );
    } catch (error) {
      this.reqStatus = 'rejected';
      this.addMessage(error.message);
    }
  },

  methods: {
    async trailer(id) {
      try {
        this.idMoviesTrailer = await fetchTrailerMovie(id);
        if (this.idMoviesTrailer) {
          this.$refs.trailerModal.open();
        }
      } catch (error) {
        this.addMessage(error.message);
      }
    },

    onWatchedClick() {
      if (this.addedMovie) {
        this.deleteWatched();
        this.addedMovie = false;
      } else {
        this.addWatched();
        this.addedMovie = true;
      }
    },

    addWatched() {
      if (this.addedMovie) {
        return;
      }
      this.toWatchArray = [...this.toWatchArray, this.movie];
      this.addedMovie = true;
    },

    deleteWatched() {
      this.toWatchArray = this.toWatchArray.filter(
        ({ id }) => id !== this.movie.id,
      );
    },
  },
  watch: {
    toWatchArray() {
      localStorage.setItem(`WATCHED`, JSON.stringify(this.toWatchArray));
    },
  },
};
</script>
