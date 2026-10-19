<template>
  <div class="mb-6 mt-8">
    <h2 class="section-title mt-6 mb-3">Let Fate Choose Your Meal!</h2>

    <v-row :dense="$vuetify.display.smAndDown">
      <!-- Panel filter -->
      <v-col :cols="$vuetify.display.smAndDown ? 12 : 8">
        <div class="spin-panel" :class="{ dark: isDark }">
          <v-icon class="spin-deco" aria-hidden="true">mdi-dice-multiple</v-icon>

          <div class="spin-content">
            <p class="spin-intro">
              Can’t decide what to cook? Spin the wheel of flavor and get a
              surprise recipe! Perfect for adventurous foodies or anyone tired
              of overthinking dinner.
            </p>

            <v-row dense class="mt-2">
              <v-col cols="12" md="4">
                <v-select
                  v-model="selectedType"
                  :items="types"
                  label="Type"
                  item-title="text"
                  item-value="value"
                  variant="solo"
                  flat
                  rounded="md"
                  density="compact"
                  hide-details
                  clearable />
              </v-col>
              <v-col cols="12" md="4">
                <v-select
                  v-model="selectedDiet"
                  :items="diets"
                  label="Diet"
                  item-title="text"
                  item-value="value"
                  variant="solo"
                  flat
                  rounded="md"
                  density="compact"
                  hide-details
                  clearable />
              </v-col>
              <v-col cols="12" md="4">
                <v-select
                  v-model="selectedCuisine"
                  :items="cuisines"
                  label="Cuisine"
                  item-title="text"
                  item-value="value"
                  variant="solo"
                  flat
                  rounded="md"
                  density="compact"
                  hide-details
                  clearable />
              </v-col>
            </v-row>

            <v-btn
              class="spin-btn mt-4"
              color="white"
              rounded="md"
              size="large"
              elevation="0"
              block
              :loading="loading"
              prepend-icon="mdi-dice-5-outline"
              @click="generateRandomRecipe">
              {{ recipe ? "Spin again" : "Generate Random Recipe" }}
            </v-btn>
          </div>
        </div>
      </v-col>

      <!-- Hasil -->
      <v-col :cols="$vuetify.display.smAndDown ? 12 : 4">
        <transition name="fade" mode="out-in">
          <!-- Loading -->
          <div v-if="loading" key="loading" class="result-card">
            <div class="result-media skeleton" />
            <div class="pa-3">
              <div class="skeleton line" />
              <div class="skeleton line short" />
            </div>
          </div>

          <!-- Hasil resep -->
          <router-link
            v-else-if="recipe"
            :key="recipe.id"
            :to="`/recipe/${recipe.id}`"
            class="result-card result-link">
            <div class="result-media">
              <img
                :src="recipe.image"
                :alt="recipe.title"
                referrerpolicy="no-referrer" />
            </div>
            <div class="pa-3">
              <p class="result-title">{{ recipe.title }}</p>
              <span class="result-cta">
                View recipe
                <v-icon size="16" class="ml-1">mdi-arrow-right</v-icon>
              </span>
            </div>
          </router-link>

          <!-- Kosong -->
          <div
            v-else-if="!$vuetify.display.smAndDown"
            key="empty"
            class="result-card empty">
            <v-icon size="40" class="mb-2" color="grey">
              mdi-help-circle-outline
            </v-icon>
            <p class="text-subtitle-1 font-weight-medium">No recipe yet</p>
            <p class="text-caption text-medium-emphasis">
              Pick your filters, then hit Generate Random Recipe
            </p>
          </div>
        </transition>
      </v-col>
    </v-row>
  </div>
</template>

<script>
  import axios from "axios";

  export default {
    name: "RandomRecipe",
    data() {
      return {
        recipe: null,
        loading: false,
        selectedType: null,
        selectedDiet: null,
        selectedCuisine: null,
        types: [
          { text: "Main course", value: "main course" },
          { text: "Side dish", value: "side dish" },
          { text: "Dessert", value: "dessert" },
          { text: "Appetizer", value: "appetizer" },
          { text: "Salad", value: "salad" },
          { text: "Bread", value: "bread" },
          { text: "Breakfast", value: "breakfast" },
          { text: "Soup", value: "soup" },
          { text: "Beverage", value: "beverage" },
          { text: "Sauce", value: "sauce" },
          { text: "Marinade", value: "marinade" },
          { text: "Fingerfood", value: "fingerfood" },
          { text: "Snack", value: "snack" },
          { text: "Drink", value: "drink" },
        ],
        diets: [
          { text: "Gluten free", value: "gluten free" },
          { text: "Ketogenic", value: "ketogenic" },
          { text: "Vegetarian", value: "vegetarian" },
          { text: "Lacto‑vegetarian", value: "lacto-vegetarian" },
          { text: "Ovo‑vegetarian", value: "ovo-vegetarian" },
          { text: "Vegan", value: "vegan" },
          { text: "Pescetarian", value: "pescetarian" },
          { text: "Paleo", value: "paleo" },
          { text: "Primal", value: "primal" },
          { text: "Low FODMAP", value: "low FODMAP" },
          { text: "Whole30", value: "whole30" },
        ],
        cuisines: [
          { text: "American", value: "American" },
          { text: "Asian", value: "Asian" },
          { text: "British", value: "British" },
          { text: "Cajun", value: "Cajun" },
          { text: "Caribbean", value: "Caribbean" },
          { text: "Chinese", value: "Chinese" },
          { text: "Eastern European", value: "Eastern European" },
          { text: "European", value: "European" },
          { text: "French", value: "French" },
          { text: "German", value: "German" },
          { text: "Greek", value: "Greek" },
          { text: "Indian", value: "Indian" },
          { text: "Irish", value: "Irish" },
          { text: "Italian", value: "Italian" },
          { text: "Japanese", value: "Japanese" },
          { text: "Jewish", value: "Jewish" },
          { text: "Korean", value: "Korean" },
          { text: "Latin American", value: "Latin American" },
          { text: "Mediterranean", value: "Mediterranean" },
          { text: "Mexican", value: "Mexican" },
          { text: "Middle Eastern", value: "Middle Eastern" },
          { text: "Nordic", value: "Nordic" },
          { text: "Southern", value: "Southern" },
          { text: "Spanish", value: "Spanish" },
          { text: "Thai", value: "Thai" },
          { text: "Vietnamese", value: "Vietnamese" },
        ],
      };
    },
    computed: {
      isDark() {
        return this.$vuetify.theme.global.name === "dark";
      },
    },
    methods: {
      async generateRandomRecipe() {
        this.loading = true;

        try {
          const params = {
            number: 1,
            apiKey: process.env.VUE_APP_SPOONACULAR_API_KEY,
          };

          if (this.selectedType) params.type = this.selectedType;
          if (this.selectedDiet) params.diet = this.selectedDiet;
          if (this.selectedCuisine) params.cuisine = this.selectedCuisine;

          const res = await axios.get(
            "https://api.spoonacular.com/recipes/complexSearch",
            { params }
          );

          this.recipe = res.data.results.length ? res.data.results[0] : null;
        } catch (error) {
          console.error("Failed to fetch random recipe:", error);
        } finally {
          this.loading = false;
        }
      },
    },
  };
</script>

<style scoped>
  .section-title {
    font-size: 1.5rem;
    font-weight: 700;
    letter-spacing: -0.01em;
  }

  /* Panel filter */
  .spin-panel {
    position: relative;
    height: 100%;
    overflow: hidden;
    padding: 24px;
    border-radius: 12px;
    background: linear-gradient(135deg, #FF4141, #ff7474);
  }
  .spin-panel.dark {
    background: linear-gradient(135deg, #FF4141, #ff7474);
  }
  .spin-deco {
    position: absolute;
    right: -18px;
    top: -18px;
    font-size: 150px !important;
    color: #fff;
    opacity: 0.14;
    transform: rotate(14deg);
    pointer-events: none;
  }
  .spin-content {
    position: relative;
  }
  .spin-intro {
    max-width: 560px;
    font-size: 15px;
    line-height: normal;
    color: #fff;
    margin-bottom: 15px;
  }
  .spin-btn {
    font-weight: 600;
    letter-spacing: normal;
    text-transform: none;
    color: #ff7474 !important;
  }

  /* Kartu hasil */
  .result-card {
    display: flex;
    flex-direction: column;
    height: 100%;
    min-height: 210px;
    overflow: hidden;
    border-radius: 12px;
    background-color: rgb(var(--v-theme-surface));
    border: 1px solid rgba(var(--v-border-color), 0.12);
    text-decoration: none;
    color: inherit;
    transition: border-color 0.2s, box-shadow 0.2s;
  }
  .result-link:hover {
    border-color: rgba(255, 120, 0, 0.5);
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
  }
  .result-media {
    aspect-ratio: 16 / 9;
    overflow: hidden;
  }
  .result-media img {
    display: block;
    width: 100%;
    height: 100%;
    object-fit: cover;
    transition: transform 0.5s ease;
  }
  .result-link:hover .result-media img {
    transform: scale(1.05);
  }
  .result-title {
    font-weight: 600;
    line-height: 1.3;
    text-transform: capitalize;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }
  .result-cta {
    display: inline-flex;
    align-items: center;
    margin-top: 6px;
    font-size: 0.85rem;
    font-weight: 600;
    color: #FF4141;
  }
  .empty {
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 16px;
    border-style: dashed;
  }

  /* Skeleton */
  .skeleton {
    background: linear-gradient(
      90deg,
      rgba(var(--v-theme-on-surface), 0.06) 25%,
      rgba(var(--v-theme-on-surface), 0.14) 50%,
      rgba(var(--v-theme-on-surface), 0.06) 75%
    );
    background-size: 200% 100%;
    animation: shimmer 1.4s infinite linear;
  }
  .skeleton.line {
    height: 12px;
    margin-bottom: 8px;
    border-radius: 6px;
  }
  .skeleton.line.short {
    width: 60%;
  }
  @keyframes shimmer {
    to {
      background-position: -200% 0;
    }
  }

  /* Transisi hasil */
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.25s, transform 0.25s;
  }
  .fade-enter-from,
  .fade-leave-to {
    opacity: 0;
    transform: translateY(6px);
  }

  @media (max-width: 959px) {
    .section-title {
      font-size: 1.25rem;
    }
    .spin-panel {
      padding: 16px;
      border-radius: 12px;
    }
    .result-card {
      border-radius: 12px;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .skeleton,
    .result-media img,
    .fade-enter-active,
    .fade-leave-active {
      animation: none;
      transition: none;
    }
  }
</style>