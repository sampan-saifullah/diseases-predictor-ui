<template>
  <v-app dark fixed app>
    <v-app-bar :clipped-left="clipped" fixed app>
      <a href="/" style="text-decoration: none">
        <v-toolbar-title v-text="title" class="text--white" />
      </a>
      <v-spacer />
      <v-btn to="/log"> Admin </v-btn>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer>
      <span>UITS Final Year Project &copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: "mdi-apps",
          title: "Welcome",
          to: "/",
        },
        {
          icon: "mdi-chart-bubble",
          title: "Inspire",
          to: "/inspire",
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: "Diseases Predictor",
    };
  },
};
</script>
