<template>
  <div class="recipe-page">
    <v-snackbar v-model="snackbar" :timeout="5000" rounded="lg">
      {{ text }}
      <template #actions>
        <v-btn
          color="#FF4141"
          variant="text"
          class="text-none"
          @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>

    <!-- Loading -->
    <v-row v-if="!recipe && !error" class="mt-2">
      <v-col cols="12" md="6">
        <div class="skeleton hero-skel" />
      </v-col>
      <v-col cols="12" md="6">
        <div class="skeleton line w60" />
        <div class="skeleton line w40" />
        <div class="skeleton block" />
      </v-col>
    </v-row>

    <!-- Error -->
    <div v-else-if="error" class="empty-state">
      <v-icon size="40" color="grey" class="mb-2">mdi-alert-circle-outline</v-icon>
      <p class="text-subtitle-1 font-weight-medium">Couldn’t load this recipe</p>
      <v-btn
        class="mt-3 text-none"
        color="primary"
        variant="flat"
        rounded="lg"
        @click="$router.back()">
        Go back
      </v-btn>
    </div>

    <template v-else>
      <v-row :class="$vuetify.display.smAndDown ? '' : 'mt-2'">
        <!-- Gambar -->
        <v-col cols="12" md="6">
          <div class="hero">
            <v-img
              :src="recipe.image"
              :alt="recipe.title"
              cover
              class="hero-img">
              <template #placeholder>
                <div class="skeleton fill" />
              </template>
            </v-img>
            <v-btn
              icon
              size="small"
              variant="flat"
              class="back-btn"
              aria-label="Back"
              @click="$router.back()">
              <v-icon>mdi-arrow-left</v-icon>
            </v-btn>
          </div>
        </v-col>

        <!-- Info utama -->
        <v-col cols="12" md="6">
          <h1 class="recipe-title">{{ recipe.title }}</h1>

          <div class="d-flex flex-wrap ga-2 mt-3">
            <v-chip v-if="recipe.glutenFree" color="green" size="small" variant="tonal">Gluten Free</v-chip>
            <v-chip v-if="recipe.dairyFree" color="blue" size="small" variant="tonal">Dairy Free</v-chip>
            <v-chip v-if="recipe.vegan" color="deep-purple" size="small" variant="tonal">Vegan</v-chip>
            <v-chip v-if="recipe.vegetarian" color="orange" size="small" variant="tonal">Vegetarian</v-chip>
          </div>

          <div class="d-flex align-center ga-2 mt-4">
            <v-btn
              color="#FF4141"
              variant="flat"
              rounded="lg"
              height="35"
              class="text-none font-weight-medium tracking-normal flex-grow-1"
              :loading="downloading"
              prepend-icon="mdi-download-outline"
              @click="downloadRecipeCard(recipe.id)">
              Download Recipe
            </v-btn>
            <v-btn
              variant="outlined"
              color="#FF4141"
              rounded="lg"
              :loading="bookmarkLogin"
              height="35"
              :aria-label="isFav ? 'Remove bookmark' : 'Bookmark recipe'"
              @click="toggleFavorite">
              <v-icon>
                {{ isFav ? "mdi-bookmark-check" : "mdi-bookmark-outline" }}
              </v-icon>
            </v-btn>
          </div>

          <!-- Statistik -->
          <v-row dense class="mt-3">
            <v-col v-for="stat in stats" :key="stat.label" cols="6" sm="3">
              <div class="stat" :style="{ '--tone': stat.tone }">
                <v-icon size="22" class="stat-icon">{{ stat.icon }}</v-icon>
                <span class="stat-label">{{ stat.label }}</span>
                <span class="stat-value">{{ stat.value }}</span>
              </div>
            </v-col>
          </v-row>

          <!-- Kontrol porsi & satuan -->
          <v-row dense class="mt-1">
            <v-col cols="12" sm="6">
              <div class="control" style="padding-top: 14px; padding-bottom: 14px">
                <span class="control-label">Servings</span>
                <div class="d-flex align-center ga-3">
                  <v-btn
                    icon
                    size="x-small"
                    variant="tonal"
                    aria-label="Fewer servings"
                    :disabled="currentServings <= 1"
                    @click="currentServings = Math.max(1, currentServings - 1)">
                    <v-icon>mdi-minus</v-icon>
                  </v-btn>
                  <span class="servings-num">{{ currentServings }}</span>
                  <v-btn
                    icon
                    size="x-small"
                    variant="tonal"
                    aria-label="More servings"
                    @click="currentServings++">
                    <v-icon>mdi-plus</v-icon>
                  </v-btn>
                </div>
              </div>
            </v-col>
            <v-col cols="12" sm="6">
              <div class="control">
                <span class="control-label">Units</span>
                <v-btn-toggle
                  v-model="scale"
                  mandatory
                  density="comfortable"
                  rounded="lg"
                  color="primary"
                  variant="outlined"
                  divided>
                  <v-btn value="metric" height="35" class="text-none">Metric</v-btn>
                  <v-btn value="us" height="35" class="text-none">US</v-btn>
                </v-btn-toggle>
              </div>
            </v-col>
          </v-row>

          <!-- Occasions -->
          <div v-if="recipe.occasions && recipe.occasions.length" class="mt-4">
            <p class="section-label mb-2">Best for</p>
            <div class="d-flex flex-wrap ga-2">
              <v-chip
                v-for="item in recipe.occasions"
                :key="item"
                size="small"
                variant="tonal"
                :prepend-icon="occasionsItems[item]"
                class="text-capitalize">
                {{ item }}
              </v-chip>
            </div>
          </div>
        </v-col>
      </v-row>

      <!-- Ringkasan -->
      <div v-if="recipe.summary" class="mt-4">
        <div
          class="summary"
          :class="{ clamped: $vuetify.display.smAndDown && !expanded }"
          v-html="recipe.summary"></div>
        <v-btn
          v-if="$vuetify.display.smAndDown"
          variant="text"
          size="small"
          class="text-none font-weight-bold px-0"
          @click="toggleExpand">
          {{ expanded ? "Show less" : "Show more" }}
          <v-icon end>{{ expanded ? "mdi-chevron-up" : "mdi-chevron-down" }}</v-icon>
        </v-btn>
      </div>

      <!-- Bahan & langkah -->
      <v-row class="mt-4 mb-4">
        <v-col cols="12" md="5">
          <div v-if="recipe.extendedIngredients" class="panel">
            <h2 class="panel-title">Ingredients</h2>
            <ul class="ingredients">
              <li v-for="ingredient in adjustedIngredients" :key="ingredient.id">
                <strong>
                  {{ formatAmountToFraction(ingredient.adjustedAmount) }}
                  {{ ingredient.adjustedUnit }}
                </strong>
                {{ ingredient.originalName }}
              </li>
            </ul>
          </div>
        </v-col>

        <v-col cols="12" md="7">
          <div v-if="recipe.analyzedInstructions?.length" class="panel">
            <h2 class="panel-title">Steps</h2>
            <ol class="steps">
              <li
                v-for="step in recipe.analyzedInstructions[0].steps"
                :key="step.number">
                <span class="step-number">{{ step.number }}</span>
                <p>{{ step.step }}</p>
              </li>
            </ol>
          </div>
        </v-col>
      </v-row>
    </template>
  </div>
</template>

<script>
  import { useFavoritesStore } from "@/stores/favoritesStore";
  import { useAuthStore } from "@/stores/authStore";
  import axios from "axios";

  export default {
    props: ["id"],

    data() {
      return {
        occasionsItems: {
          fall: "mdi-leaf-maple",
          winter: "mdi-snowflake",
          summer: "mdi-weather-sunny",
          spring: "mdi-leaf",
        },
        bookmarkLogin: false,
        downloading: false,
        currentServings: 1,
        scale: "metric",
        recipe: null,
        error: false,
        expanded: false,
        snackbar: false,
        text: "",
      };
    },

    methods: {
      async downloadRecipeCard(id) {
        this.downloading = true;
        try {
          const { data } = await axios.get(
            `https://api.spoonacular.com/recipes/${id}/card`,
            { params: { apiKey: process.env.VUE_APP_SPOONACULAR_API_KEY } }
          );
          window.open(data.url, "_blank", "noopener");
        } catch (error) {
          console.error("Error downloading recipe card:", error);
          this.text = "Couldn’t download the recipe card. Try again.";
          this.snackbar = true;
        } finally {
          this.downloading = false;
        }
      },

      async toggleFavorite() {
        const authStore = useAuthStore();
        const favoritesStore = useFavoritesStore();

        if (!authStore.user) {
          this.$router.push("/login");
          return;
        }

        this.bookmarkLogin = true;
        try {
          if (favoritesStore.isFavorite(this.recipe.id)) {
            await favoritesStore.removeFavorite(this.recipe.id);
            this.text = "You have removed the recipe from your bookmarks";
          } else {
            await favoritesStore.addFavorite(this.recipe);
            this.text = "You have bookmarked the recipe";
          }
          this.snackbar = true;
        } finally {
          this.bookmarkLogin = false;
        }
      },

      centsToUSD(cents) {
        return new Intl.NumberFormat("en-US", {
          style: "currency",
          currency: "USD",
        }).format(cents / 100);
      },

      toggleExpand() {
        this.expanded = !this.expanded;
      },

      formatAmountToFraction(amount) {
        const fractionMap = {
          0.125: "⅛",
          0.25: "¼",
          0.333: "⅓",
          0.375: "⅜",
          0.5: "½",
          0.625: "⅝",
          0.666: "⅔",
          0.75: "¾",
          0.875: "⅞",
        };

        const rounded = Math.round(amount * 1000) / 1000;
        const whole = Math.floor(rounded);
        const decimal = parseFloat((rounded - whole).toFixed(3));
        let bestMatch = "";
        for (const [val, symbol] of Object.entries(fractionMap)) {
          if (Math.abs(decimal - parseFloat(val)) < 0.02) {
            bestMatch = symbol;
            break;
          }
        }

        if (bestMatch && whole > 0) return `${whole}${bestMatch}`;
        if (bestMatch && whole === 0) return bestMatch;
        return rounded.toString();
      },
    },

    async created() {
      const apiKey = process.env.VUE_APP_SPOONACULAR_API_KEY;
      const url = `https://api.spoonacular.com/recipes/${this.id}/information?apiKey=${apiKey}`;
      try {
        const response = await axios.get(url);
        this.recipe = response.data;
        this.currentServings = this.recipe.servings;
      } catch (error) {
        console.error("Error fetching recipe details:", error);
        this.error = true;
      }
    },

    computed: {
      isFav() {
        if (!this.recipe) return false;
        const store = useFavoritesStore();
        return store.isFavorite(this.recipe.id);
      },

      stats() {
        if (!this.recipe) return [];
        return [
          {
            icon: "mdi-clock-outline",
            label: "Time",
            value: `${this.recipe.readyInMinutes} min`,
            tone: "255, 179, 0",
          },
          {
            icon: "mdi-heart-outline",
            label: "Health score",
            value: this.recipe.healthScore,
            tone: "244, 67, 54",
          },
          {
            icon: "mdi-star-outline",
            label: "Score",
            value: `${Math.round(this.recipe.spoonacularScore)}%`,
            tone: "156, 39, 176",
          },
          {
            icon: "mdi-currency-usd",
            label: "Per serving",
            value: this.centsToUSD(this.recipe.pricePerServing),
            tone: "255, 120, 0",
          },
        ];
      },

      adjustedIngredients() {
        if (!this.recipe?.extendedIngredients) return [];

        return this.recipe.extendedIngredients.map((ingredient) => {
          const originalServings = this.recipe.servings;
          const ratio = this.currentServings / originalServings;

          const baseMeasure =
            this.scale == "metric"
              ? ingredient.measures.metric
              : ingredient.measures.us;

          const adjustedAmount = baseMeasure.amount * ratio;

          return {
            ...ingredient,
            adjustedAmount,
            adjustedUnit: baseMeasure.unitShort,
          };
        });
      },
    },
  };
</script>

<style scoped>
  .recipe-page {
    padding: 16px;
  }

  /* Hero */
  .hero {
    position: relative;
    border-radius: 16px;
    overflow: hidden;
  }
  .hero-img {
    aspect-ratio: 4 / 3;
  }
  .back-btn {
    position: absolute;
    top: 12px;
    left: 12px;
    z-index: 1;
    color: #fff;
    background: rgba(0, 0, 0, 0.4) !important;
    backdrop-filter: blur(6px);
    -webkit-backdrop-filter: blur(6px);
  }

  .recipe-title {
    font-size: 1.7rem;
    font-weight: 700;
    line-height: 1.2;
    letter-spacing: -0.01em;
    text-wrap: balance;
  }

  /* Statistik */
  .stat {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
    padding: 12px 8px;
    border-radius: 12px;
    background-color: rgba(var(--tone), 0.12);
  }
  .stat-icon {
    color: rgb(var(--tone));
    margin-bottom: 2px;
  }
  .stat-label {
    font-size: 0.8rem;
    color: rgba(var(--v-theme-on-surface), 0.7);
  }
  .stat-value {
    font-size: 1rem;
    font-weight: 700;
  }

  /* Kontrol */
  .control {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
    padding: 10px 14px;
    border-radius: 12px;
    border: 1px solid rgba(var(--v-border-color), 0.12);
  }
  .control-label,
  .section-label {
    font-size: 0.9rem;
    font-weight: 600;
  }
  .servings-num {
    min-width: 24px;
    text-align: center;
    font-size: 1.1rem;
    font-weight: 700;
  }

  /* Ringkasan */
  .summary {
    max-width: 90ch;
    line-height: 1.6;
    color: rgba(var(--v-theme-on-surface), 0.8);
  }
  .summary.clamped {
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }
  .summary :deep(a) {
    color: #FF4141;
  }

  /* Panel bahan & langkah */
  .panel {
    height: 100%;
    padding: 20px;
    border-radius: 16px;
    background-color: rgb(var(--v-theme-surface));
    border: 1px solid rgba(var(--v-border-color), 0.12);
  }
  .panel-title {
    margin-bottom: 12px;
    font-size: 1.15rem;
    font-weight: 700;
  }

  .ingredients {
    list-style: none;
    padding: 0;
    margin: 0;
  }
  .ingredients li {
    padding: 9px 0;
    font-size: 0.95rem;
    border-bottom: 1px dashed rgba(var(--v-border-color), 0.25);
  }
  .ingredients li:last-child {
    border-bottom: none;
  }

  .steps {
    list-style: none;
    padding: 0;
    margin: 0;
  }
  .steps li {
    display: flex;
    gap: 12px;
    padding-bottom: 16px;
  }
  .steps li:last-child {
    padding-bottom: 0;
  }
  .steps p {
    font-size: 0.95rem;
    line-height: 1.55;
    padding-top: 2px;
  }
  .step-number {
    flex: 0 0 auto;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 26px;
    height: 26px;
    border-radius: 50%;
    font-size: 0.8rem;
    font-weight: 700;
    color: #fff;
    background-color: #FF4141;
  }

  /* Skeleton & empty */
  .skeleton {
    background: linear-gradient(
      90deg,
      rgba(var(--v-theme-on-surface), 0.06) 25%,
      rgba(var(--v-theme-on-surface), 0.14) 50%,
      rgba(var(--v-theme-on-surface), 0.06) 75%
    );
    background-size: 200% 100%;
    animation: shimmer 1.4s infinite linear;
    border-radius: 12px;
  }
  .skeleton.fill {
    width: 100%;
    height: 100%;
    border-radius: 0;
  }
  .hero-skel {
    aspect-ratio: 4 / 3;
    border-radius: 16px;
  }
  .skeleton.line {
    height: 18px;
    margin-bottom: 12px;
  }
  .skeleton.block {
    height: 160px;
    margin-top: 16px;
  }
  .w60 {
    width: 60%;
  }
  .w40 {
    width: 40%;
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
    padding: 64px 16px;
    text-align: center;
  }

  @media (max-width: 959px) {
    .recipe-page {
      padding: 12px;
    }
    .hero {
      border-radius: 12px;
    }
    .hero-img {
      aspect-ratio: 16 / 10;
    }
    .recipe-title {
      font-size: 1.35rem;
    }
    .panel {
      padding: 16px;
      border-radius: 12px;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .skeleton {
      animation: none;
    }
  }
</style>