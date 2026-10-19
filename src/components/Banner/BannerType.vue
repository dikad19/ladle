<template>
  <v-row class="mt-8" :dense="$vuetify.display.smAndDown">
    <v-col cols="12" class="pb-0">
      <h2 class="section-title">Meal Time</h2>
    </v-col>

    <v-col v-for="(meal, i) in meals" :key="meal.type" cols="12">
      <article
        class="meal-card"
        :class="{ reverse: i % 2 === 1 }"
        tabindex="0"
        role="link"
        @click="handleList(meal.type)"
        @keydown.enter="handleList(meal.type)">
        <div class="meal-media">
          <v-img :src="meal.img" cover class="meal-img" :alt="meal.title">
            <template #placeholder>
              <div class="skeleton" />
            </template>
          </v-img>
        </div>

        <div class="meal-body">
          <v-chip
            size="small"
            variant="tonal"
            color="primary"
            class="mb-3 font-weight-medium">
            {{ meal.label }}
          </v-chip>
          <h3 class="meal-title">{{ meal.title }}</h3>
          <p class="meal-desc">{{ meal.description }}</p>
          <span class="meal-link">
            See {{ meal.label.toLowerCase() }} recipes
            <v-icon size="18" class="ml-1">mdi-arrow-right</v-icon>
          </span>
        </div>
      </article>
    </v-col>
  </v-row>
</template>

<script>
  export default {
    name: "MealTimeComponent",

    data() {
      return {
        meals: [
          {
            type: "dinner",
            label: "Dinner",
            title: "Hearty Dinner Recipes to End Your Day Right",
            description:
              "End your day with comforting, satisfying meals made from simple ingredients. These dinner recipes are perfect for winding down, whether you’re cooking for family or enjoying a solo night in. From cozy stews to flavorful stir-fries, discover dishes that bring warmth to your evenings.",
            img: "https://img.spoonacular.com/recipes/641975-636x393.jpg",
          },
          {
            type: "lunch",
            label: "Lunch",
            title: "Quick and Tasty Lunch Ideas for Midday Energy",
            description:
              "Fuel your afternoon with these easy and delicious lunch recipes. Designed for busy schedules and hungry appetites, these meals offer a balance of nutrition and flavor — perfect for workdays, school, or weekend relaxation. Think wraps, bowls, salads, and everything in between!",
            img: "https://img.spoonacular.com/recipes/632502-636x393.jpg",
          },
          {
            type: "breakfast",
            label: "Breakfast",
            title: "Bright Mornings: Breakfast Recipes to Start Fresh",
            description:
              "Jumpstart your day with energizing and wholesome breakfast dishes. Whether you prefer something sweet, savory, light, or hearty — these recipes are made to brighten your mornings and keep you full until lunch. Pancakes, eggs, smoothies, and more await!",
            img: "https://img.spoonacular.com/recipes/637792-636x393.jpg",
          },
        ],
      };
    },

    methods: {
      handleList(type) {
        this.$router.push(`/recipe-list/${type}`);
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

  .meal-card {
    display: grid;
    grid-template-columns: 5fr 6fr;
    overflow: hidden;
    cursor: pointer;
    border-radius: 12px;
    background-color: rgb(var(--v-theme-surface));
    border: 1px solid rgba(var(--v-border-color), 0.12);
    transition: border-color 0.2s, box-shadow 0.2s;
  }
  .meal-card:hover,
  .meal-card:focus-visible {
    border-color: #FF4141;
    box-shadow: 0 2px 12px #ff414141;
    outline: none;
  }
  /* Selang-seling posisi gambar di desktop */
  .meal-card.reverse .meal-media {
    order: 2;
  }

  .meal-media {
    position: relative;
    overflow: hidden;
    min-height: 240px;
  }
  .meal-img {
    position: absolute;
    inset: 0;
    height: 100%;
    transition: transform 0.5s ease;
  }
  .meal-card:hover .meal-img {
    transform: scale(1.05);
  }

  .skeleton {
    width: 100%;
    height: 100%;
    background: linear-gradient(
      90deg,
      rgba(var(--v-theme-on-surface), 0.06) 25%,
      rgba(var(--v-theme-on-surface), 0.14) 50%,
      rgba(var(--v-theme-on-surface), 0.06) 75%
    );
    background-size: 200% 100%;
    animation: shimmer 1.4s infinite linear;
  }
  @keyframes shimmer {
    to {
      background-position: -200% 0;
    }
  }

  .meal-body {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    justify-content: center;
    padding: 28px 32px;
  }
  .meal-title {
    font-size: 1.3rem;
    font-weight: 700;
    line-height: 1.25;
    text-wrap: balance;
  }
  .meal-desc {
    margin-top: 10px;
    max-width: 62ch;
    line-height: 1.55;
    font-size: 15px;
    color: rgb(155, 155, 155);
    display: -webkit-box;
    -webkit-line-clamp: 4;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }
  .meal-link {
    display: inline-flex;
    align-items: center;
    margin-top: 16px;
    font-size: 0.95rem;
    font-weight: 600;
    color: #FF4141;
  }
  .meal-link .v-icon {
    transition: transform 0.2s;
  }
  .meal-card:hover .meal-link .v-icon {
    transform: translateX(4px);
  }

  @media (max-width: 959px) {
    .section-title {
      font-size: 1.25rem;
    }
    .meal-card {
      grid-template-columns: 1fr;
      border-radius: 12px;
    }
    .meal-card.reverse .meal-media {
      order: 0;
    }
    .meal-media {
      min-height: 0;
      aspect-ratio: 16 / 9;
    }
    .meal-body {
      padding: 16px;
    }
    .meal-title {
      font-size: 1.1rem;
    }
    .meal-desc {
      -webkit-line-clamp: 3;
      font-size: 0.9rem;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .meal-img,
    .skeleton,
    .meal-link .v-icon {
      animation: none;
      transition: none;
    }
  }
</style>