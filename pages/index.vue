<template>
  <div class="container">
    <div class="centered-element">
      <v-row>
        <v-col cols="12">
          <v-select
            :items="symptomsItems"
            label="Select your symptoms .."
            item-text="name"
            item-value="_id"
            v-model="symptoms"
            multiple
            outlined
          ></v-select>
        </v-col>
        <v-col cols="12">
          <v-btn
            block
            :disabled="symptoms.length === 0"
            :loading="loading"
            @click="predict"
          >
            Search
          </v-btn>
        </v-col>
        <v-col cols="12" v-if="show">
          <v-card class="pa-2 orange darken-2 text-center">
            <div v-if="diseases.length === 0">
              <span>No diseases matched</span>
              <v-btn class="ma-2" rounded @click="doctorDialogForMbbs">
                M.B.B.S
              </v-btn>
            </div>
            <div v-else>
              <span class="font-italic text-xl">Predicted diseases</span>
              <v-expansion-panels>
                <v-expansion-panel v-for="d in diseases" :key="d._id">
                  <v-expansion-panel-header v-if="d.matches >= 25">
                    {{ d.name }}
                    <v-progress-circular
                      :value="d.matches"
                      :color="
                        d.matches >= 75
                          ? 'red'
                          : d.matches >= 50
                          ? 'orange'
                          : 'green'
                      "
                      :size="60"
                      :width="5"
                    >
                      {{ d.matches }} %</v-progress-circular
                    >
                  </v-expansion-panel-header>
                  <v-expansion-panel-content>
                    <v-btn class="ma-2" rounded @click="doctorDialog(d)">
                      {{ d.specalist.name }}
                    </v-btn>
                  </v-expansion-panel-content>
                </v-expansion-panel>
              </v-expansion-panels>
            </div>
          </v-card>
        </v-col>
      </v-row>
    </div>
    <v-dialog v-model="dialog" width="500">
      <v-card>
        <v-card-title class="text-h5 orange lighten-1">
          {{ doctor.specalist }}
        </v-card-title>

        <v-card-text v-for="d in this.doctor.list" :key="d._id" class="py-4">
          <div>{{ d.name }} / {{ d.phone }}</div>
          <div>Contact - {{ d.description }}</div>
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="dialog = false">Ok</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  data: () => ({
    data: [],
    symptomsItems: [],
    symptoms: [],
    loading: false,
    diseases: [],
    show: false,
    dialog: false,
    doctor: {
      specalist: "",
      list: [],
    },
    mbbs: {
      specalist: {},
    },
  }),
  mounted() {
    this.load();
    this.loadMbbsSpecialist();
  },
  methods: {
    async loadMbbsSpecialist() {
      const data = await this.$axios.get(`/specialist/list/M.B.B.S`);
      this.mbbs.specalist = data.data[0];
    },
    doctorDialogForMbbs() {
      this.doctorDialog(this.mbbs);
    },
    async doctorDialog(d) {
      console.log(d);
      this.doctor.specalist = d.specalist.name;
      this.doctor.list = [];
      const data = await this.$axios.get(`/doctor/${d.specalist._id}`);
      this.doctor.list = data.data;
      this.dialog = true;
    },
    async load() {
      const d = await this.$axios.get("/symptom");
      this.symptomsItems = d.data;
    },
    async predict() {
      try {
        this.loading = true;
        const d = await this.$axios.post("/diseases/predictor/engine", {
          symptoms: this.symptoms,
        });
        this.loading = false;
        this.diseases = d.data;
        this.show = true;
      } catch (error) {
        this.loading = false;
      }
    },
  },
};
</script>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {
  position: relative;
  height: 100%;
}

.centered-element {
  margin: 0;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>