<template>
  <v-card color="basil">
    <v-card-title class="text-center justify-center py-6">
      <h1 class="font-weight-bold text-h5 text-md-h2 basil--text">
        Admin Panel
      </h1>
    </v-card-title>

    <v-tabs v-model="tab" background-color="orange" color="basil" grow>
      <v-tab v-for="item in items" :key="item">
        {{ item }}
      </v-tab>
    </v-tabs>

    <v-tabs-items v-model="tab">
      <v-tab-item v-for="item in items" :key="item">
        <diseases v-if="item === 'Diseases'" />
        <doctor v-if="item === 'Doctors'" />
        <symptoms v-if="item === 'Symptoms'" />
        <specialist v-if="item === 'Specialists'" />
      </v-tab-item>
    </v-tabs-items>
  </v-card>
</template>
<script>
import doctor from "../components/doctor.vue";
import diseases from "../components/diseases.vue";
import symptoms from "../components/symptom.vue";
import specialist from "../components/specialist.vue";
export default {
  data() {
    return {
      tab: null,
      items: ["Diseases", "Symptoms", "Doctors", "Specialists"],
      text: "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
    };
  },
  components: {
    doctor,
    diseases,
    symptoms,
    specialist,
  },
};
</script>
<style>
/* Helper classes */
.basil {
  background-color: #fffbe6 !important;
}
.basil--text {
  color: #356859 !important;
}
</style>