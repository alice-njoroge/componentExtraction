<script setup>
import {reactive, ref, watch} from "vue";

const props = defineProps({
  modelValue: {
    required: false,
    type: Object
  },
  errors: {
    required: false,
    type: Object
  }
});
const _form = ref({...props.modelValue}); //copy of props
watch(
    () => props.modelValue,
    (newValue) => _form.value = {...newValue}
)

const genres = reactive([
  {text: "Drama", value: "Drama"},
  {text: "Crime", value: "Crime"},
  {text: "Action", value: "Action"},
  {text: "Comedy", value: "Comedy"},
]);

const emits = defineEmits({'update:modelValue': null})
const handleSubmit = () =>{
  emits('update:modelValue', {..._form.value}); //copy of props
}
</script>

<template>
  <div class="modal-wrapper-inner">
    <form @submit.prevent="handleSubmit">
      <div class="movie-form-input-wrapper">
        <label for="name">Name</label>
        <input
            type="text"
            name="name"
            v-model="_form.name"
            class="movie-form-input"
        />
        <span class="movie-form-error">{{ errors.name }}</span>
      </div>
      <div class="movie-form-input-wrapper">
        <label for="description">Description</label>
        <textarea
            type="text"
            name="description"
            v-model="_form.description"
            class="movie-form-textarea"
        />
        <span class="movie-form-error">{{ errors.description }}</span>
      </div>
      <div class="movie-form-input-wrapper">
        <label for="image">Image</label>
        <input
            type="text"
            name="image"
            v-model="_form.image"
            class="movie-form-input"
        />
        <span class="movie-form-error">{{ errors.image }}</span>
      </div>
      <div class="movie-form-input-wrapper">
        <label for="genre">Genres</label>
        <select
            name="genre"
            v-model="_form.genres"
            class="movie-form-input"
            multiple
        >
          <option
              v-for="option in genres"
              :key="option.value"
              :value="option.value"
          >
            {{ option.text }}
          </option>
        </select>
        <span class="movie-form-error">
              {{ errors.genres }}
            </span>
      </div>
      <div class="movie-form-input-wrapper">
        <label for="genre" class="movie-form-checkbox-label">
          <input
              type="checkbox"
              v-model="_form.inTheaters"
              :true-value="true"
              :false-value="false"
              class="movie-form-checkbox"
          />
          <span>In theaters</span>
        </label>
        <span class="movie-form-error">
              {{ errors.inTheaters }}
            </span>
      </div>
      <div class="movie-form-actions-wrapper">
        <button type="button" class="button" @click="$emit('hideForm')">
          Cancel
        </button>

        <button type="submit" class="button-primary">
          <span v-if="_form.id">Update</span>
          <span v-else>Create</span>
        </button>
      </div>
    </form>
  </div>

</template>


<style scoped>

</style>