<template>
  <!-- Bar utama -->
  <v-app-bar app flat class="nav-main" height="64">
    <div class="nav-container">
      <div class="d-flex align-center">
        <v-btn
          v-if="$vuetify.display.smAndDown"
          icon
          variant="text"
          size="small"
          class="mr-2"
          aria-label="Toggle menu"
          @click.stop="drawer = !drawer">
          <v-icon>{{ drawer ? "mdi-close" : "mdi-menu" }}</v-icon>
        </v-btn>

        <v-img
          src="/assets/logo.png"
          class="logo"
          max-height="34"
          min-width="34"
          max-width="120"
          @click="handleHome" />

        <nav v-if="!$vuetify.display.smAndDown" class="d-flex align-center ml-6 ga-1">
          <button
            v-for="link in mainLinks"
            :key="link.to"
            class="nav-link"
            :class="{ active: isActive(link.to) }"
            @click="$router.push(link.to)">
            {{ link.name }}
          </button>
        </nav>
      </div>

      <div class="d-flex align-center ga-2">
        <v-autocomplete
          v-if="!$vuetify.display.smAndDown"
          v-model="selectedRecipe"
          :items="results"
          :loading="loading"
          item-title="title"
          item-value="id"
          return-object
          no-filter
          hide-no-data
          hide-details
          density="compact"
          variant="solo-filled"
          flat
          rounded="pill"
          class="search-field"
          prepend-inner-icon="mdi-magnify"
          menu-icon=""
          placeholder="Search recipe or ingredient"
          @keydown.enter="searchRecipes"
          @update:search="onSearch"
          @update:model-value="onSelect" />

        <!-- Jangan akses authStore langsung jika masih null -->
        <template v-if="authStore && authStore.initialized">
          <v-menu
            v-if="user && user.photoURL && user.displayName"
            location="bottom end"
            offset="8">
            <template #activator="{ props }">
              <v-btn
                v-bind="props"
                variant="tonal"
                rounded="pill"
                class="profile-btn px-2">
                <v-avatar size="26" class="mr-2">
                  <v-img :src="user.photoURL" referrerpolicy="no-referrer" alt="User Photo" />
                </v-avatar>
                <span class="text-body-2 font-weight-medium">{{ firstName }}</span>
                <v-icon size="18" class="ml-1">mdi-chevron-down</v-icon>
              </v-btn>
            </template>

            <v-list min-width="240" rounded="lg" class="pa-2 menu-list">
              <v-list-item rounded="lg" @click="toProfile">
                <template #prepend>
                  <v-avatar size="36" class="mr-3">
                    <v-img :src="user.photoURL" />
                  </v-avatar>
                </template>
                <v-list-item-title class="font-weight-bold">
                  {{ user.displayName }}
                </v-list-item-title>
                <v-list-item-subtitle>{{ user.email }}</v-list-item-subtitle>
              </v-list-item>
              <v-divider class="my-2" />
              <v-list-item
                rounded="lg"
                prepend-icon="mdi-logout"
                title="Logout"
                class="logout-item"
                @click="logout" />
            </v-list>
          </v-menu>

          <v-btn
            v-else
            color="primary"
            variant="flat"
            rounded="pill"
            class="text-none font-weight-medium"
            @click="handleLogin">
            Login
          </v-btn>
        </template>

        <v-btn
          icon
          variant="text"
          size="small"
          aria-label="Toggle theme"
          @click="toggleTheme">
          <v-icon>{{ isDark ? "mdi-white-balance-sunny" : "mdi-weather-night" }}</v-icon>
        </v-btn>
      </div>
    </div>
  </v-app-bar>

  <!-- Bar kedua: kategori (desktop) / search (mobile) -->
  <v-app-bar
    app
    flat
    scroll-behavior="hide"
    class="nav-sub"
    :height="$vuetify.display.smAndDown ? 60 : 44">
    <div class="nav-container">
      <div v-if="!$vuetify.display.smAndDown" class="d-flex align-center ga-2">
        <v-chip
          v-for="item in headerAdditional"
          :key="item.value"
          :to="undefined"
          variant="text"
          size="small"
          class="category-chip"
          @click="handleList(item.value)">
          {{ item.name }}
        </v-chip>
      </div>

      <v-autocomplete
        v-else
        v-model="selectedRecipe"
        :items="results"
        :loading="loading"
        item-title="title"
        item-value="id"
        return-object
        no-filter
        hide-no-data
        hide-details
        density="compact"
        variant="solo-filled"
        flat
        rounded="pill"
        prepend-inner-icon="mdi-magnify"
        menu-icon=""
        placeholder="Search recipe or ingredient"
        @keydown.enter="searchRecipes"
        @update:search="onSearch"
        @update:model-value="onSelect" />
    </div>
  </v-app-bar>

  <v-navigation-drawer v-model="drawer" temporary>
    <v-list nav density="comfortable">
      <v-list-item
        v-for="link in mainLinks"
        :key="link.to"
        :prepend-icon="link.icon"
        :title="link.name"
        rounded="lg"
        @click="$router.push(link.to)" />
      <v-list-group value="Recipes">
        <template #activator="{ props }">
          <v-list-item v-bind="props" prepend-icon="mdi-silverware-fork-knife" title="Recipes" rounded="lg" />
        </template>
        <v-list-item
          v-for="item in headerAdditional"
          :key="item.value"
          :title="item.name"
          rounded="lg"
          @click="handleList(item.value)" />
      </v-list-group>
    </v-list>
  </v-navigation-drawer>
</template>

<script>
  import { useRecipeStore } from "@/stores/recipeSearch";
  import { useAuthStore } from "@/stores/authStore";
  import axios from "axios";

  export default {
    name: "NavbarComponent",

    data() {
      return {
        selectedRecipe: null,
        query: "",
        results: [],
        loading: false,
        cancelToken: null,
        drawer: false,
        authStore: useAuthStore(),
        search: "",
        mainLinks: [
          { name: "Home", to: "/", icon: "mdi-home-outline" },
          { name: "Blog", to: "/blog", icon: "mdi-book-open-outline" },
        ],
        headerAdditional: [
          { value: "dinner", name: "Dinner" },
          { value: "easy", name: "Quick & Easy" },
          { value: "healthy", name: "Healthy" },
          { value: "tasty", name: "Tasty" },
        ],
      };
    },

    computed: {
      isDark() {
        return this.$vuetify.theme.global.name === "dark";
      },
      user() {
        return this.authStore.user;
      },
      firstName() {
        return (this.user?.displayName || "").split(" ")[0];
      },
    },

    mounted() {
      this.authStore = useAuthStore();
      const savedTheme = localStorage.getItem("theme");
      if (savedTheme) {
        this.$vuetify.theme.global.name = savedTheme;
      } else {
        const prefersDark = window.matchMedia(
          "(prefers-color-scheme: dark)"
        ).matches;
        this.$vuetify.theme.global.name = prefersDark ? "dark" : "light";
      }
    },

    methods: {
      isActive(path) {
        return path === "/"
          ? this.$route.path === "/"
          : this.$route.path.startsWith(path);
      },

      toHome() {
        this.$router.push("/");
      },

      toBlog() {
        this.$router.push("/blog");
      },

      async handleList(id) {
        const store = useRecipeStore();
        await store.fetchRecipes(this.query);
        this.$router.push(`/recipe-list/${id}`);
      },

      commitBlankData() {
        const blank = this.blankData;
        Object.entries(blank).forEach(([key, value]) => {
          this.$store.commit(`customerHolder/${key}`, value);
        });
      },

      toProfile() {
        this.$router.push("/profile");
      },

      handleHome() {
        this.$router.push("/");
      },

      handleLogin() {
        this.$router.push("/login");
      },

      async onSearch(query) {
        this.query = query;

        if (query.length < 4) {
          this.results = [];
          return;
        }

        if (this.cancelToken) {
          this.cancelToken.cancel("Cancel previous request");
        }

        this.cancelToken = axios.CancelToken.source();
        this.loading = true;

        try {
          const res = await axios.get(
            `https://api.spoonacular.com/recipes/autocomplete`,
            {
              params: {
                number: 5,
                query,
                apiKey: process.env.VUE_APP_SPOONACULAR_API_KEY,
              },
              cancelToken: this.cancelToken.token,
            }
          );
          this.results = res.data;
        } catch (err) {
          if (!axios.isCancel(err)) {
            console.error("Autocomplete error:", err);
          }
        } finally {
          this.loading = false;
        }
      },

      logout() {
        const store = useAuthStore();
        store.logout().then(() => {
          this.$router.push("/login");
        });
      },

      onSelect(item) {
        if (item?.id) {
          this.selectedRecipe = null;
          this.$router.push(`/recipe/${item.id}`);
        }
      },

      toggleTheme() {
        const next = this.isDark ? "light" : "dark";
        this.$vuetify.theme.global.name = next;
        localStorage.setItem("theme", next);
      },

      async searchRecipes() {
        const store = useRecipeStore();
        await store.fetchRecipes(this.query);
        this.$router.push(`/recipe-list/${this.query}`);
      },
    },
  };
</script>

<style scoped>
  .nav-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    max-width: 1140px;
    margin: 0 auto;
    padding: 0 16px;
  }

  /* Semi-transparan + blur, tanpa shadow tebal */
  .nav-main,
  .nav-sub {
    background-color: rgba(var(--v-theme-surface), 0.85) !important;
    backdrop-filter: saturate(180%) blur(12px);
    -webkit-backdrop-filter: saturate(180%) blur(12px);
    box-shadow: none !important;
  }
  .nav-main {
    border-bottom: 1px solid rgba(var(--v-border-color), 0.08);
  }
  /* Satu-satunya shadow: tipis di bawah bar kedua */
  .nav-sub {
    border-bottom: 1px solid rgba(var(--v-border-color), 0.12);
    box-shadow: 0 1px 8px rgba(0, 0, 0, 0.05) !important;
  }

  .logo {
    cursor: pointer;
    flex: 0 0 auto;
  }

  .nav-link {
    position: relative;
    padding: 6px 12px;
    font-size: 15px;
    font-weight: 500;
    border-radius: 8px;
    color: rgba(var(--v-theme-on-surface), 0.72);
    transition: background-color 0.15s, color 0.15s;
  }
  .nav-link:hover {
    background-color: rgba(var(--v-theme-on-surface), 0.06);
    color: rgb(var(--v-theme-on-surface));
  }
  .nav-link.active {
    color: #FF4141;
    background-color: rgba(255, 120, 0, 0.1);
  }

  .search-field {
    width: 340px;
  }
  .search-field :deep(.v-field),
  .nav-sub :deep(.v-field) {
    --v-field-padding-start: 14px;
    background-color: rgba(var(--v-theme-on-surface), 0.05);
    transition: background-color 0.15s, box-shadow 0.15s;
  }
  .search-field :deep(.v-field--focused),
  .nav-sub :deep(.v-field--focused) {
    background-color: rgb(var(--v-theme-surface));
    box-shadow: 0 0 0 2px rgba(255, 120, 0, 0.45);
  }

  .profile-btn {
    text-transform: none;
    letter-spacing: normal;
  }

  .menu-list {
    border: 1px solid rgba(var(--v-border-color), 0.12);
  }
  .logout-item {
    color: #FF4141;
  }

  .category-chip {
    cursor: pointer;
    font-size: 14px;
    font-weight: 500;
    color: rgba(var(--v-theme-on-surface), 0.72);
  }
  .category-chip:hover {
    color: #FF4141;
    background-color: rgba(255, 120, 0, 0.08);
  }
</style>