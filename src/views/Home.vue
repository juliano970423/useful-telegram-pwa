<template>
  <div class="md3e-root">
    <NavBar />

    <main class="md3e-main">
      <div class="layout-container">
        <div class="hero-section">
          <div class="hero-content">
            <div class="hero-image" />
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import NavBar from '@/components/NavBar.vue'
</script>

<style scoped>
/* Material Design 3 Expressive Color Scheme */
:root {
  --md3-primary: #6750A4;
  --md3-on-primary: #FFFFFF;
  --md3-primary-container: #EADDFF;
  --md3-on-primary-container: #21005D;
  --md3-secondary: #625B71;
  --md3-on-secondary: #FFFFFF;
  --md3-secondary-container: #E8DEF8;
  --md3-on-secondary-container: #1D192B;
  --md3-tertiary: #7D5260;
  --md3-on-tertiary: #FFFFFF;
  --md3-tertiary-container: #FFD8E4;
  --md3-on-tertiary-container: #31111D;
  --md3-surface: #FFFBFE;
  --md3-on-surface: #1C1B1F;
  --md3-surface-variant: #E7E0EC;
  --md3-on-surface-variant: #49454F;
  --md3-outline: #79747E;
  --md3-outline-variant: #CAC4D0;
  --md3-shadow: #000000;
  --md3-scrim: #000000;
  --md3-error: #B3261E;
  --md3-on-error: #FFFFFF;
  --md3-error-container: #F9DEDC;
  --md3-on-error-container: #410E0B;

  /* Custom theme colors based on reference */
  --theme-primary: #6750A4;
  --theme-on-primary: #FFFFFF;
  --theme-surface: #FFFFFF;
  --theme-on-surface: #111418;
  --theme-on-surface-variant: #617589;
  --theme-outline: #f0f2f4;
}

.md3e-root {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background-color: var(--md3-surface);
  color: var(--md3-on-surface);
  font-family: "Roboto", "Segoe UI", sans-serif;
}

.md3e-main {
  flex: 1;
  padding: 1.25rem 6.25rem; /* px-40 equivalent */
  display: flex;
  justify-content: center; /* Center content horizontally */
}

.layout-container {
  max-width: 60rem; /* max-w-[960px] */
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.hero-section {
  width: 100%;
  display: flex;
  justify-content: center;
}

.hero-content {
  width: 100%;
  container-type: inline-size;
}

@container (min-width: 480px) {
  .hero-content {
    padding: 1rem;
  }
}

.hero-image {
  width: 100%;
  height: 70vh; /* Make the image take 70% of viewport height */
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  overflow: hidden;
  background-color: var(--md3-surface);
  border-radius: 0.75rem; /* rounded-xl */
  background-image: url("https://lh3.googleusercontent.com/aida-public/AB6AXuAbvR7SnLyjf2CHpHTSSxE6aqXdP52HR5OCrRV_mCCUXRQYX1O212hi6NWlj8fp7uf_86gKP0AxGzc9cpnJbm5fSemnKRkrimJO9G98W9olQGlUmDU7KOOOZ0lvRwXGMn71etGLLm2bio_fPHylgGlzrRcLC7DH-Fr6VGiUEIMDE7iI5eV9g9zlLLbWAcYq_7wwWATwZbwKI08LZ3nxEJCUTda8YFrFDLtYiG5MGnU5Cx6j6C_k_kZSu2OLSOb5QeMJQZOruD4ixiqV");
}

/* Material Design 3 Typography Scales */
.display-large {
  font-size: 3.562rem;
  line-height: 4rem;
  font-weight: 400;
  letter-spacing: -0.015625em;
}

.display-medium {
  font-size: 2.812rem;
  line-height: 3.25rem;
  font-weight: 400;
  letter-spacing: 0em;
}

.display-small {
  font-size: 2.25rem;
  line-height: 2.75rem;
  font-weight: 400;
  letter-spacing: 0em;
}

.headline-large {
  font-size: 2rem;
  line-height: 2.5rem;
  font-weight: 400;
  letter-spacing: 0em;
}

.headline-medium {
  font-size: 1.5rem;
  line-height: 2rem;
  font-weight: 400;
  letter-spacing: 0.0075em;
}

.headline-small {
  font-size: 1.25rem;
  line-height: 1.75rem;
  font-weight: 400;
  letter-spacing: 0.0125em;
}

.title-large {
  font-size: 1.375rem;
  line-height: 1.75rem;
  font-weight: 400;
  letter-spacing: 0.0125em;
}

.title-medium {
  font-size: 1rem;
  line-height: 1.5rem;
  font-weight: 500;
  letter-spacing: 0.0075em;
}

.title-small {
  font-size: 0.875rem;
  line-height: 1.25rem;
  font-weight: 500;
  letter-spacing: 0.0075em;
}

.label-large {
  font-size: 0.875rem;
  line-height: 1.25rem;
  font-weight: 500;
  letter-spacing: 0.00625em;
}

.label-medium {
  font-size: 0.75rem;
  line-height: 1rem;
  font-weight: 500;
  letter-spacing: 0.03125em;
}

.label-small {
  font-size: 0.6875rem;
  line-height: 1rem;
  font-weight: 500;
  letter-spacing: 0.03125em;
}

.body-large {
  font-size: 1rem;
  line-height: 1.5rem;
  font-weight: 400;
  letter-spacing: 0.03125em;
}

.body-medium {
  font-size: 0.875rem;
  line-height: 1.25rem;
  font-weight: 400;
  letter-spacing: 0.015625em;
}

.body-small {
  font-size: 0.75rem;
  line-height: 1rem;
  font-weight: 400;
  letter-spacing: 0.025em;
}

.text-center {
  text-align: center;
}

/* Responsive design */
@media (max-width: 1024px) {
  .md3e-main {
    padding: 1.25rem 2rem;
  }
}

@media (max-width: 768px) {
  .md3e-main {
    padding: 1rem 1rem;
  }
}

@media (max-width: 480px) {
  .md3e-main {
    padding: 0.5rem;
  }
}
</style>
