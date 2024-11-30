<template>
  <v-app>
    <v-app-bar app color="#f2e2ce" absolute flat>
      <div class="headline"><strong>DIY Wearable Biosensing</strong></div>
    </v-app-bar>

    <v-main>
      <v-container fluid>
        <router-view></router-view>
      </v-container>
    </v-main>

    <v-footer padless app absolute color="#f2e2ce" class="mt-7">
      <v-col
        class="text-center"
        cols="12"
      >
        {{ new Date().getFullYear() }} — <strong>DIY Wearable Biosensing</strong>
      </v-col>
    </v-footer>

  </v-app>
</template>

<script>
import '@/assets/style/global.scss'
export default {
  name: "App",

  data: () => ({
    //
  }),
};
</script>

<style>
  .v-application{
    .headline {
      font-family: "Montserrat", sans-serif !important;
    }
  }
  .theme--light.v-application {
      color: #030303 !important
  }
  .theme--light.v-subheader {
      color: #030303 !important
  }
  .theme--light.v-sheet {
      color: #030303 !important
  }
  .v-card__title {
      color: #030303 !important
  }
</style>