<template>
  <v-row :dense="$vuetify.display.smAndDown" class="mt-2">
    <!-- Hero carousel -->
    <v-col :cols="$vuetify.display.smAndDown ? 12 : 8">
      <div
        class="hero"
        @mouseenter="paused = true"
        @mouseleave="paused = false">
        <v-carousel
          :cycle="!paused"
          :interval="4000"
          height="100%"
          show-arrows="hover"
          hide-delimiter-background
          delimiter-icon="mdi-circle"
          class="hero-carousel">
          <v-carousel-item
            v-for="slide in carouselData"
            :key="slide.type"
            :src="slide.img"
            cover
            class="cursor-pointer"
            @click="handleList(slide.type)">
            <div class="hero-overlay">
              <v-chip
                size="small"
                variant="flat"
                color="primary"
                class="mb-2 font-weight-medium text-capitalize">
                {{ slide.type }}
              </v-chip>
              <h2 class="hero-title">{{ slide.name }}</h2>
              <p v-if="!$vuetify.display.xs" class="hero-desc">
                {{ slide.description }}
              </p>
            </div>
          </v-carousel-item>

          <template #prev="{ props }">
            <v-btn
              v-bind="props"
              icon="mdi-chevron-left"
              variant="flat"
              size="small"
              class="arrow-btn" />
          </template>
          <template #next="{ props }">
            <v-btn
              v-bind="props"
              icon="mdi-chevron-right"
              variant="flat"
              size="small"
              class="arrow-btn" />
          </template>
        </v-carousel>
      </div>
    </v-col>

    <!-- Kartu samping -->
    <v-col :cols="$vuetify.display.smAndDown ? 12 : 4">
      <v-row :dense="$vuetify.display.smAndDown" class="side-row">
        <v-col
          v-for="item in sideData"
          :key="item.type"
          :cols="$vuetify.display.smAndDown ? 6 : 12">
          <div class="card side-card" @click="handleList(item.type)">
            <v-img :src="item.img" cover class="card-img">
              <template #placeholder>
                <div class="skeleton" />
              </template>
            </v-img>
            <div class="side-overlay">
              <span class="side-title">{{ item.name }}</span>
              <v-icon size="18" color="white" class="side-arrow">
                mdi-arrow-top-right
              </v-icon>
            </div>
          </div>
        </v-col>
      </v-row>
    </v-col>
  </v-row>
</template>

<script>
  export default {
    name: "CarouselComponent",

    data() {
      return {
        paused: false,
        carouselData: [
          {
            img: "https://img.spoonacular.com/recipes/659021-636x393.jpg",
            name: "Simple Salads That Taste Amazing",
            description:
              "Discover easy-to-make salads that are both refreshing and packed with flavor—perfect for any meal or snack.",
            type: "salad",
          },
          {
            img: "https://img.spoonacular.com/recipes/660820-636x393.jpg",
            name: "The Ultimate 10 Spaghetti Recipe You Need to Try",
            description:
              "From creamy to classic, explore our top 10 spaghetti recipes that will satisfy your cravings and impress your guests.",
            type: "spaghetti",
          },
          {
            img: "https://img.spoonacular.com/recipes/646191-636x393.jpg",
            name: "Quick Sandwich Ideas for Busy Days",
            description:
              "Need a fast and tasty bite? These sandwich ideas are perfect for hectic mornings, work lunches, or on-the-go meals.",
            type: "sandwich",
          },
        ],
        sideData: [
          {
            img: "https://img.spoonacular.com/recipes/646561-636x393.jpg",
            name: "Easy and Delicious Homemade Soups",
            type: "soup",
          },
          {
            img: "https://img.spoonacular.com/recipes/638729-636x393.jpg",
            name: "Easy Homemade Fried Rice You’ll Love",
            type: "rice",
          },
        ],
      };
    },

    methods: {
      handleList(id) {
        this.$router.push(`/recipe-category/${id}`);
      },
    },
  };
</script>

<style scoped>
  /* Wrapper menentukan tinggi lewat aspect-ratio, v-carousel mengisi 100% */
  .hero {
    aspect-ratio: 16 / 9;
    width: 100%;
    overflow: hidden;
    border-radius: 16px;
    isolation: isolate;
  }
  .hero-carousel {
    height: 100%;
  }

  /* Bullet: tanpa background, di kanan bawah */
  .hero :deep(.v-carousel__controls) {
    justify-content: flex-end;
    padding-right: 16px;
    padding-bottom: 6px;
    background: transparent;
  }
  .hero :deep(.v-carousel__controls .v-btn) {
    color: #fff;
    opacity: 0.5;
    --v-btn-size: 0.45rem;
  }
  .hero :deep(.v-carousel__controls .v-btn--active) {
    opacity: 1;
  }

  /* Panah kaca buram */
  .arrow-btn {
    color: #fff !important;
    background: rgba(0, 0, 0, 0.35) !important;
    backdrop-filter: blur(6px);
    -webkit-backdrop-filter: blur(6px);
  }
  .arrow-btn:hover {
    background: rgba(0, 0, 0, 0.55) !important;
  }

  .card {
    position: relative;
    width: 100%;
    overflow: hidden;
    border-radius: 16px;
    cursor: pointer;
    text-align: left;
    isolation: isolate;
    background-color: rgba(var(--v-theme-on-surface), 0.08);
  }
  .side-card {
    aspect-ratio: 16 / 8.77;
  }

  .card-img {
    position: absolute;
    inset: 0;
    height: 100%;
    transition: transform 0.5s ease;
  }
  .card:hover .card-img {
    transform: scale(1.04);
  }

  /* Shimmer saat gambar loading */
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

  /* Hero overlay */
  .hero-overlay {
    position: absolute;
    inset: auto 0 0 0;
    z-index: 1;
    padding: 56px 24px 22px;
    padding-right: 120px;
    color: #fff;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    text-align: left;
    background: linear-gradient(
      to top,
      rgba(0, 0, 0, 0.78) 0%,
      rgba(0, 0, 0, 0.4) 55%,
      transparent 100%
    );
  }
  .hero-title {
    font-size: 1.6rem;
    font-weight: 700;
    line-height: 1.2;
    letter-spacing: -0.01em;
    max-width: 30ch;
    text-wrap: balance;
  }
  .hero-desc {
    margin-top: 6px;
    max-width: 56ch;
    font-size: 0.95rem;
    line-height: 1.45;
    color: rgba(255, 255, 255, 0.85);
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }

  /* Side overlay */
  .side-overlay {
    position: absolute;
    inset: auto 0 0 0;
    z-index: 1;
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    gap: 12px;
    padding: 36px 16px 14px;
    background: linear-gradient(to top, rgba(0, 0, 0, 0.72), transparent);
  }
  .side-title {
    color: #fff;
    font-size: 1rem;
    font-weight: 600;
    line-height: 1.3;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }
  .side-arrow {
    flex: 0 0 auto;
    padding: 4px;
    box-sizing: content-box;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.2);
    transition: background 0.2s, transform 0.2s;
  }
  .side-card:hover .side-arrow {
    background: rgb(var(--v-theme-primary));
    transform: translate(2px, -2px);
  }

  .side-row {
    height: 100%;
  }

  @media (max-width: 959px) {
    .hero {
      border-radius: 12px;
    }
    .hero-overlay {
      padding: 40px 16px 16px;
      padding-right: 90px;
    }
    .hero-title {
      font-size: 1.15rem;
    }
    .side-title {
      font-size: 0.85rem;
    }
    .side-overlay {
      padding: 28px 12px 10px;
    }
    .card {
      border-radius: 12px;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .card-img,
    .skeleton,
    .side-arrow {
      animation: none;
      transition: none;
    }
  }
</style>