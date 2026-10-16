<template>
  <v-row :dense="$vuetify.display.smAndDown">
    <!-- Hero carousel -->
    <v-col :cols="$vuetify.display.smAndDown ? 12 : 8">
      <Carousel
        class="hero"
        :autoplay="4000"
        :wrap-around="true"
        :pause-autoplay-on-hover="true">
        <Slide v-for="slide in carouselData" :key="slide.type">
          <div class="card hero-card" @click="handleList(slide.type)">
            <v-img :src="slide.img" cover class="card-img">
              <template #placeholder>
                <div class="skeleton" />
              </template>
            </v-img>

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
          </div>
        </Slide>

        <template #addons>
          <Navigation />
          <Pagination />
        </template>
      </Carousel>
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
  import { Carousel, Slide, Pagination, Navigation } from "vue3-carousel";

  export default {
    name: "CarouselComponent",

    components: {
      Carousel,
      Slide,
      Pagination,
      Navigation,
    },

    data() {
      return {
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

<style>
  /* Style library vue3-carousel (harus non-scoped) */
  .hero .carousel__viewport {
    border-radius: 16px;
  }

  .hero .carousel__pagination {
    position: absolute;
    right: 16px;
    bottom: 14px;
    margin: 0;
    padding: 0;
    width: auto;
    gap: 6px;
  }
  .hero .carousel__pagination-button {
    width: 22px;
    height: 22px;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .hero .carousel__pagination-button::after {
    content: "";
    width: 8px;
    height: 8px;
    border-radius: 999px;
    background-color: rgba(255, 255, 255, 0.45);
    transition: width 0.25s, background-color 0.25s;
  }
  .hero .carousel__pagination-button--active::after {
    width: 22px;
    background-color: #fff;
  }

  /* Panah: kaca buram, muncul saat hover (desktop) */
  .hero .carousel__prev,
  .hero .carousel__next {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: rgba(0, 0, 0, 0.35);
    backdrop-filter: blur(6px);
    -webkit-backdrop-filter: blur(6px);
    opacity: 0;
    transition: opacity 0.2s, background 0.2s;
  }
  .hero .carousel__prev {
    left: 12px;
  }
  .hero .carousel__next {
    right: 12px;
  }
  .hero:hover .carousel__prev,
  .hero:hover .carousel__next {
    opacity: 1;
  }
  .hero .carousel__prev:hover,
  .hero .carousel__next:hover {
    background: rgba(0, 0, 0, 0.55);
  }
  .hero .carousel__icon {
    fill: #fff !important;
  }

  @media (hover: none) {
    .hero .carousel__prev,
    .hero .carousel__next {
      display: none;
    }
  }
</style>

<style scoped>
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

  .hero-card {
    aspect-ratio: 16 / 9;
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

  /* Dua kartu samping tinggi-nya mengikuti hero di desktop */
  .side-row {
    height: 100%;
  }

  @media (max-width: 959px) {
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
    .card,
    .hero .carousel__viewport {
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