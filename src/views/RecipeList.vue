<template>
  <div>
    <div class="list-head mt-2 mb-4">
      <h1 class="title-list">
        Recipe list for <span class="accent">{{ id }}</span>
      </h1>
      <p v-if="!loading && totalResults" class="result-count">
        {{ totalResults.toLocaleString() }} recipes
      </p>
    </div>

    <!-- Loading -->
    <v-row v-if="loading" class="mb-4">
      <v-col v-for="n in 8" :key="n" cols="12" sm="6" md="3">
        <div class="skeleton media-skel" />
        <div class="skeleton line mt-3" />
        <div class="skeleton line short" />
      </v-col>
    </v-row>

    <!-- Kosong -->
    <div v-else-if="!recipes.length" class="empty-state mb-6">
      <v-icon size="40" color="grey" class="mb-2">mdi-silverware-fork-knife</v-icon>
      <p class="text-subtitle-1 font-weight-medium">No recipes found</p>
      <p class="text-caption text-medium-emphasis">
        Try another category or search term
      </p>
      <v-btn
        class="mt-3 text-none"
        color="primary"
        variant="flat"
        rounded="lg"
        @click="$router.push('/')">
        Back to home
      </v-btn>
    </div>

    <!-- Daftar -->
    <v-row v-else class="mb-4">
      <v-col
        v-for="recipe in recipes"
        :key="recipe.uniqueId"
        cols="12"
        sm="6"
        md="3">
        <article
          class="recipe-card"
          tabindex="0"
          role="link"
          @click="handleToDetails(recipe.id)"
          @keydown.enter="handleToDetails(recipe.id)">
          <div class="recipe-media">
            <v-img
              :src="recipe.image"
              :alt="recipe.title"
              cover
              class="recipe-img">
              <template #placeholder>
                <div class="skeleton fill" />
              </template>
            </v-img>
          </div>
          <h3 class="title-text">{{ recipe.title }}</h3>
        </article>
      </v-col>
    </v-row>

    <v-pagination
      v-if="totalPages > 1"
      v-model="currentPage"
      class="mt-4 mb-6"
      :length="totalPages"
      :total-visible="$vuetify.display.smAndDown ? 5 : 7"
      density="comfortable"
      rounded="lg"
      active-color="primary" />
  </div>
</template>

<script>
  import { useRecipeStore } from "@/stores/recipeSearch";

  export default {
    props: {
      id: String,
      page: {
        type: Number,
        default: 1,
      },
    },
    data() {
      return {
        recipes: [],
        loading: false,
        currentPage: 1,
        perPage: 12,
        totalResults: 0,
      };
    },
    computed: {
      totalPages() {
        return Math.ceil(this.totalResults / this.perPage);
      },
    },
    watch: {
      "$route.query.page": {
        immediate: true,
        handler(newPage) {
          this.currentPage = parseInt(newPage) || 1;
          this.fetchRecipesById(this.id, this.currentPage);
        },
      },
      id(newId) {
        this.$router.push({
          name: "RecipeList",
          params: { id: newId },
          query: { page: 1 },
        });
      },
      currentPage(newPage) {
        if (parseInt(this.$route.query.page) !== newPage) {
          this.$router.push({
            name: "RecipeList",
            params: { id: this.id },
            query: { page: newPage },
          });
        }
      },
    },
    methods: {
      handleToDetails(id) {
        this.$router.push(`/recipe/${id}`);
      },
      async fetchRecipesById(id, page) {
        this.loading = true;
        try {
          const store = useRecipeStore();
          await store.fetchRecipes(id, page, this.perPage);
          this.totalResults = store.totalResults;

          this.recipes = store.recipes.map((recipe, index) => ({
            ...recipe,
            uniqueId: recipe.id || `recipe-${index}`,
          }));
        } catch (err) {
          console.error("Failed to fetch recipes:", err);
          this.recipes = [];
          this.totalResults = 0;
        } finally {
          this.loading = false;
          window.scrollTo({ top: 0, behavior: "smooth" });
        }
      },
    },
  };
</script>

<style scoped>
  .list-head {
    display: flex;
    align-items: baseline;
    justify-content: space-between;
    gap: 12px;
    flex-wrap: wrap;
  }
  .title-list {
    font-size: 1.6rem;
    font-weight: 700;
    letter-spacing: -0.01em;
  }
  .accent {
    color: #FF4141;
    text-transform: capitalize;
  }
  .result-count {
    font-size: 0.9rem;
    color: rgba(var(--v-theme-on-surface), 0.6);
  }

  /* Kartu */
  .recipe-card {
    height: 100%;
    overflow: hidden;
    cursor: pointer;
    border-radius: 12px;
    background-color: rgb(var(--v-theme-surface));
    border: 1px solid rgba(var(--v-border-color), 0.12);
    transition: border-color 0.2s, box-shadow 0.2s;
  }
  .recipe-card:hover,
  .recipe-card:focus-visible {
    border-color: rgba(255, 120, 0, 0.5);
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
    outline: none;
  }
  .recipe-media {
    position: relative;
    aspect-ratio: 16 / 10;
    overflow: hidden;
  }
  .recipe-img {
    position: absolute;
    inset: 0;
    height: 100%;
    transition: transform 0.5s ease;
  }
  .recipe-card:hover .recipe-img {
    transform: scale(1.05);
  }
  .title-text {
    padding: 12px 14px 14px;
    font-size: 0.95rem;
    font-weight: 600;
    line-height: 1.3;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }

  /* Skeleton & empty */
  .skeleton {
    border-radius: 12px;
    background: linear-gradient(
      90deg,
      rgba(var(--v-theme-on-surface), 0.06) 25%,
      rgba(var(--v-theme-on-surface), 0.14) 50%,
      rgba(var(--v-theme-on-surface), 0.06) 75%
    );
    background-size: 200% 100%;
    animation: shimmer 1.4s infinite linear;
  }
  .skeleton.fill {
    width: 100%;
    height: 100%;
    border-radius: 0;
  }
  .media-skel {
    aspect-ratio: 16 / 10;
    border-radius: 16px;
  }
  .skeleton.line {
    height: 14px;
    margin-bottom: 8px;
  }
  .skeleton.line.short {
    width: 60%;
  }
  @keyframes shimmer {
    to {
      background-position: -200% 0;
    }
  }

  .empty-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 56px 16px;
    text-align: center;
    border: 1px dashed rgba(var(--v-border-color), 0.2);
    border-radius: 16px;
  }

  @media (max-width: 959px) {
    .title-list {
      font-size: 1.3rem;
    }
    .recipe-card {
      border-radius: 12px;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .recipe-img,
    .skeleton {
      animation: none;
      transition: none;
    }
  }
</style>