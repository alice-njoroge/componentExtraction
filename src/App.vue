<script setup>
import { computed, reactive, ref } from "vue";
/*
These are Icons that you can use, of course you can use other ones if you prefer.
*/
import { items } from "./movies.json";
import MovieItem from "@/components/MovieItem.vue";
import MovieForm from "@/components/movieForm.vue";

const movies = ref(items);

function updateRating(id, rating) {
  const index = movies.value.findIndex(iteratee => iteratee.id === id);
  movies.value[index].rating = rating;
}
function removeMovie(id) {
  movies.value = movies.value.filter(iteratee => iteratee.id !== id);
}
function editMovie(id) {
  const index = movies.value.findIndex(iteratee => iteratee.id === id);
  const movie = movies.value[index];

  form.id = movie.id;
  form.name = movie.name;
  form.description = movie.description;
  form.image = movie.image;
  form.inTheaters = movie.inTheaters;
  form.genres = movie.genres;

  showForm();
}

const errors = reactive({
  name: null,
  description: null,
  image: null,
  inTheaters: null,
  genres: null,
});
const form = reactive({
  id: null,
  name: null,
  description: null,
  image: null,
  inTheaters: false,
  genres: null,
});
const validations = reactive({
  name: "required",
  genres: "required",
});


const validationRules = (rule) => {
  if (rule === "required") return /^ *$/;

  return null;
};

function validate() {
  let valid = true;
  clearErrors();
  for (const [field, rule] of Object.entries(validations)) {
    const validation = validationRules(rule);
    if (validation) {
      if (validation.test(form[field] || "")) {
        errors[field] = `${field} is ${rule}`;
        valid = false;
      }
    }
  }

  return valid;
}

function saveMovie(payload) {
  form.id = payload.id;
  form.name = payload.name;
  form.description = payload.description;
  form.image = payload.image;
  form.inTheaters = payload.inTheaters;
  form.genres = payload.genres;

  if (form.id) {
    updateMovie();
  } else {
    addMovie();
  }
}

function updateMovie() {
  if (validate()) {
    const movie = {
      id: form.id,
      name: form.name,
      description: form.description,
      image: form.image,
      genres: form.genres,
      inTheaters: form.inTheaters,
      rating: 0,
    };

    movies.value = movies.value.map((m) => {
      if (m.id === movie.id) {
        movie.rating = m.rating;
        return movie;
      }
      return m;
    });

    hideForm();
  }
}

function addMovie() {
  if (validate()) {
    const movie = {
      id: Number(Date.now()),
      name: form.name,
      description: form.description,
      image: form.image,
      genres: form.genres,
      inTheaters: form.inTheaters,
      rating: 0,
    };
    movies.value.push(movie);
    hideForm();
  }
}

function cleanUpForm() {
  form.name = null;
  form.description = null;
  form.image = null;
  form.genres = null;
  form.inTheaters = false;
  clearErrors();
}

function clearErrors() {
  errors.name = null;
  errors.description = null;
  errors.image = null;
  errors.genres = null;
  errors.inTheaters = null;
}

const showMovieForm = ref(false);
function hideForm() {
  showMovieForm.value = false;
  cleanUpForm();
}

function showForm() {
  showMovieForm.value = true;
}

const averageRating = computed(() => {
  const avg = movies.value
    .map((movie) => parseInt(movie.rating || 0))
    .reduce((a, b) => a + b, 0);

  return Number(avg / movies.value.length).toFixed(1);
});

const totalMovies = computed(() => {
  return movies.value.length;
});

function removeRatings() {
  movies.value = movies.value.map((movie) => {
    movie.rating = 0;
    return movie;
  });
}
</script>

<template>
  <div class="app">
    <div v-if="showMovieForm" class="modal-wrapper">
      <MovieForm
          :model-value="form"
          :errors="errors"
          @update:modelValue="saveMovie"
          @hideForm="hideForm"
      />
    </div>
    <div class="movie-actions-list-wrapper">
      <div class="movie-actions-list-info">
        <span>Total Movies: {{ totalMovies }}</span>
        <span> / </span>
        <span>Average Rating: {{ averageRating }}</span>
      </div>
      <div class="flex-spacer"></div>
      <div class="movie-actions-list-actions">
        <button
          class="self-end movie-actions-list-action-button button-primary justify-self-end"
          @click="removeRatings"
        >
          Remove Ratings
        </button>
        <button
          class="movie-actions-list-action-button"
          :class="{
            'button-primary': !showMovieForm,
            'button-disabled': showMovieForm,
          }"
          @click="showForm"
          :disabled="showMovieForm"
        >
          Add Movie
        </button>
      </div>
    </div>
    <div class="movie-list">
      <MovieItem
        v-for="(movie) in movies"
        :key="movie.id"
        :movie="movie"
        @remove="removeMovie"
        @edit="editMovie"
        @update:rating="updateRating"
      >
      </MovieItem>
    </div>
  </div>
</template>
