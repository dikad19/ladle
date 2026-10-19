<template>
  <v-app>
    <NavbarComponent />

    <v-main>
      <div style="max-width: 1140px" class="pa-4 mx-auto h-100 w-100">
        <router-view />
      </div>
    </v-main>

    <v-footer class="app-footer">
      <div class="footer-inner">
        <div class="d-flex align-center ga-3">
          <v-img
            src="/assets/logo-painted.png"
            max-height="28"
            max-width="28"
            min-width="28"
            class="cursor-pointer"
            alt="Logo"
            @click="$router.push('/')" />
          <span class="footer-text">© {{ year }} All rights reserved</span>
        </div>

        <nav class="d-flex align-center ga-1">
          <router-link to="/" class="footer-link">Home</router-link>
          <router-link to="/blog" class="footer-link">Blog</router-link>
        </nav>

        <span class="footer-text">
          Recipes by
          <a
            href="https://spoonacular.com/food-api"
            target="_blank"
            rel="noopener"
            class="footer-link inline">
            Spoonacular
          </a>
        </span>
      </div>
    </v-footer>
  </v-app>
</template>

<script>
  import NavbarComponent from "@/components/Navbar";

  export default {
    name: "App",
    components: {
      NavbarComponent,
    },
    computed: {
      isDark() {
        return this.$vuetify.theme.global.name === "dark"
      },
      logoSrc() {
        return this.isDark ? "/assets/logo.png" : "/assets/logo-black.png"
      },
      year() {
        return new Date().getFullYear();
      },
    },
  };
</script>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100..900;1,100..900&display=swap");
</style>

<style scoped>
  .app-footer {
    padding: 0;
    border-top: 1px solid rgba(var(--v-border-color), 0.12);
    background-color: rgb(var(--v-theme-surface)) !important;
  }
  
  .footer-inner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 12px 24px;
    width: 100%;
    max-width: 1140px;
    margin: 0 auto;
    padding: 20px 16px;
  }
  
  .footer-text {
    font-size: 0.85rem;
    color: rgba(var(--v-theme-on-surface), 0.6);
  }
  
  .footer-link {
    padding: 4px 10px;
    font-size: 0.9rem;
    font-weight: 500;
    border-radius: 8px;
    text-decoration: none;
    color: rgba(var(--v-theme-on-surface), 0.72);
    transition: background-color 0.15s, color 0.15s;
  }
  
  .footer-link:hover {
    color: #ff7800;
    background-color: rgba(255, 120, 0, 0.1);
  }
  
  .footer-link.inline {
    padding: 0;
    font-size: inherit;
    font-weight: 600;
    color: #ff7800;
    background: none;
  }
  
  .footer-link.inline:hover {
    text-decoration: underline;
    background: none;
  }

  @media (max-width: 599px) {
    .footer-inner {
      flex-direction: column;
      align-items: flex-start;
    }
  }
</style>
