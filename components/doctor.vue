<template>
  <v-data-table :headers="headers" :items="desserts" class="elevation-1">
    <template v-slot:top>
      <v-toolbar flat>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn dark class="mb-2" v-bind="attrs" v-on="on"> Add </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="12">
                    <v-text-field
                      v-model="editedItem.name"
                      label="name"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="12">
                    <v-text-field
                      v-model="editedItem.phone"
                      label="phone"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="12">
                    <v-text-field
                      v-model="editedItem.description"
                      label="address"
                    ></v-text-field>
                  </v-col>
                  <v-col class="d-flex" cols="12">
                    <v-select
                      :items="specalistItems"
                      label="Specialist"
                      item-text="name"
                      item-value="_id"
                      return-object
                      v-model="editedItem.specalist"
                    ></v-select>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close"> Cancel </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
                :disabled="
                  editedItem.name === '' || editedItem.specalist === ''
                "
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete"
                >Cancel</v-btn
              >
              <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                >OK</v-btn
              >
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
      <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
    </template>
    <template v-slot:no-data> No data found </template>
  </v-data-table>
</template>
<script>
export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      { text: "Name", value: "name", width: "20%" },
      { text: "Specialist", value: "specalist.name", width: "20%" },
      { text: "Contact", value: "phone", width: "10%" },
      { text: "Address", value: "description", width: "40%" },
      { text: "Actions", value: "actions", sortable: false },
    ],
    desserts: [],
    specalistItems: [],
    editedIndex: -1,
    editedItem: {
      name: "",
      specalist: "",
      phone: "",
      description: "",
      _id: 0,
    },
    defaultItem: {
      name: "",
      specalist: "",
      phone: "",
      description: "",
      _id: 0,
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Item" : "Edit Item";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  mounted() {
    this.initialize();
    this.specalist();
  },

  methods: {
    specalist() {
      this.$axios.get("/specialist").then((r) => {
        this.specalistItems = r.data;
      });
    },
    initialize() {
      this.$axios
        .get("/doctor")
        .then((response) => {
          this.desserts = response.data;
          this.editedItem = {};
        })
        .catch(function (error) {
          console.log(error);
        });
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    async deleteItemConfirm() {
      await this.$axios.delete(`/doctor/${this.editedItem._id}`);
      this.closeDelete();
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.initialize();
    },

    save() {
      if (this.editedIndex > -1) {
        this.$axios
          .post(`/doctor/${this.editedItem._id}`, {
            name: this.editedItem.name,
            phone: this.editedItem.phone,
            specalist: this.editedItem.specalist._id,
            description: this.editedItem.description,
          })
          .then((s) => {
            this.initialize();
          });
      } else {
        this.$axios
          .post("/doctor", {
            name: this.editedItem.name,
            phone: this.editedItem.phone,
            specalist: this.editedItem.specalist._id,
            description: this.editedItem.description,
          })
          .then((s) => {
            this.initialize();
          });
      }
      this.close();
    },
  },
};
</script>