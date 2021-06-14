<template>
  <div class="tw-p-16 bg">
    <div class="tw-w-full">
      <div class="tw-w-full tw-h-full">
        <div class="tw-flex tw-flex-col tw-w-full tw-h-1/2">
          <h1>Notes</h1>

          <div
            class="
              tw-w-72
              tw-bg-white
              tw-rounded-md
              tw-border-gray-300
              tw-mr-5
              tw-mt-2
              tw-h-72
            "
          >
            {{ note }}
            <div class="tw-flex tw-justify-between tw-p-2">
              <v-text-field v-model="note.title"></v-text-field>
              <div class="tw-flex">
                <div
                  class="
                    tw-bg-blue-500
                    tw-cursor-pointer
                    tw-w-1/2
                    tw-h-5
                    tw-self-center
                    tw-mx-8
                    tw-rounded-sm
                  "
                  @click="saveNote()"
                >
                  <div>
                    <h1>Add</h1>
                  </div>
                </div>
              </div>
            </div>
            <v-textarea v-model="note.text" rows="5"></v-textarea>
          </div>

          <div class="tw-pt-5 tw-w-full tw-flex tw-flex-wrap">
            <div
              class="
                tw-w-72
                tw-bg-white
                tw-rounded-md
                tw-border-gray-300
                tw-mr-5
                tw-mt-2
                tw-h-72
              "
              v-for="Note in Notes"
              :key="Note"
            >
              <div class="tw-flex tw-justify-between tw-p-2">
                <v-text-field :value="Note.title">{{Note.title}}</v-text-field>
                <div class="tw-flex">
                  <div
                    class="
                      tw-bg-red-500
                      tw-cursor-pointer
                      tw-mx-2
                      tw-p-2
                      tw-rounded-sm
                    "
                  >
                    <h1>Save</h1>
                  </div>
                  <div
                    class="
                      tw-bg-yellow-200 tw-cursor-pointer tw-p-2 tw-rounded-sm
                    "
                  >
                    <h1>Delete</h1>
                  </div>
                </div>
              </div>
              <v-textarea rows="7" :value="Note.text"> </v-textarea>
            </div>
          </div>
        </div>
      </div>
      <div class="tw-flex tw-flex-row tw-my-4">
        <div class="tw-w-1/2 tw-h-full">
          <h1 class="tw-my-2">Tasks</h1>

          <div class="tw-flex tw-h-14">
            <v-text-field class="tw-" outlined> </v-text-field>

            <div
              class="
                tw-bg-blue-500 tw-cursor-pointer tw-mx-2 tw-p-4 tw-rounded-sm
              "
            >
              <h1>Add</h1>
            </div>
          </div>
          <div
            class="
              tw-flex
              tw-justify-between
              tw-items-center
              tw-p-1
              tw-h-14
              tw-my-4
              tw-bg-white
            "
          >
            <v-checkbox v-model="checkbox">
              <template v-slot:label>
                <div>
                  <v-text-field> </v-text-field>
                </div>
              </template>
            </v-checkbox>

            <div class="tw-w-1/4">
              <v-text-field label="Due date" type="date"></v-text-field>
            </div>
            <div class="tw-w-1/4">
              <v-icon small class="mr-2" @click="editItem(item)">
                mdi-pencil
              </v-icon>
              <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
            </div>
          </div>
          <div
            class="
              tw-flex
              tw-justify-between
              tw-items-center
              tw-p-1
              tw-h-14
              tw-my-4
              tw-bg-white
            "
          >
            <v-checkbox v-model="checkbox">
              <template v-slot:label>
                <div>
                  <v-text-field> </v-text-field>
                </div>
              </template>
            </v-checkbox>

            <div class="tw-w-1/4">
              <v-text-field label="Due date" type="date"></v-text-field>
            </div>
            <div class="tw-w-1/4">
              <v-icon small class="mr-2" @click="editItem(item)">
                mdi-pencil
              </v-icon>
              <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
            </div>
          </div>
        </div>
        <div class="tw-w-1/2 tw-p-3 tw-h-full">
          <h1>Shopping List</h1>

          <v-card>
            <div class="tw-flex tw-h-14">
              <v-text-field class="tw-w-3/4 tw-p-4" label="Item" outlined>
              </v-text-field>
              <v-text-field class="tw-w-1/4 tw-p-4" label="Price" outlined>
              </v-text-field>

              <div
                class="
                  tw-bg-blue-500 tw-cursor-pointer tw-mx-2 tw-p-4 tw-rounded-sm
                "
              >
                <h1>Add</h1>
              </div>
            </div>
            <v-card-title>
              <v-text-field
                v-model="search"
                append-icon="mdi-magnify"
                label="Search"
                single-line
                class="elevation-0"
                hide-details
              ></v-text-field>
            </v-card-title>
            <v-data-table
              :headers="headers"
              :items="shoppingList"
              :search="search"
            >
              <template v-slot:item.isCompeleted="{ item }">
                <v-checkbox v-model="item.isCompeleted">
                  <template v-slot:label>
                    <div>
                      <v-text-field
                        :class="item.isCompeleted ? 'tw-line-through' : ''"
                        v-model="item.title"
                      >
                        disabled
                      </v-text-field>
                    </div>
                  </template>
                </v-checkbox>
              </template>
              <template v-slot:item.actions="{ item }">
                <v-icon small class="mr-2" @click="editItem(item)">
                  mdi-pencil
                </v-icon>
                <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
              </template>
            </v-data-table>
          </v-card>
        </div>
      </div>

      pop
    </div>
  </div>
</template>


<script>
export default {
  methods: {
    async saveNote() {
      this.note.id = this.Notes.length + 2;
      let res = await this.$axios.post("/note", {
        ...this.note,
      });
      this.getNotes();
    },
    async getNotes() {
      let res = await this.$axios.get("/note");
      console.log(res.data);
      this.Notes = [...res.data];
    },
  },
  async created() {
    await this.getNotes();
  },
  data() {
    return {
      note: {
        id: 15,
        title: "",
        text: "",
      },
      Notes: [],
      shoppingList: [
        {
          isCompeleted: true,
          title: "Buying some milk",
          price: 2.645,
        },
        {
          isCompeleted: false,
          title: "Buying some milk",
          price: 2.645,
        },
        {
          isCompeleted: false,
          title: "Buying some milk",
          price: 2.645,
        },
      ],
      headers: [
        {
          text: "isCompeleted",
          align: "start",
          sortable: false,
          value: "isCompeleted",
        },
        { text: "price", value: "price" },
        { text: "", value: "actions" },
      ],
    };
  },
};
</script>

<style scoped>
</style>
