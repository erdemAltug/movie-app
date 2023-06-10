<template>
  <div style="color: #1e1e1e">
    <v-toolbar
      src="https://cdn.vuetifyjs.com/images/backgrounds/vbanner.jpg"
      elevation="24"
      color="toolbarColor"
      class="pt-4"
      prominent
      dense
      text
      height="100px"
    >
      <v-col cols="1">
        <v-btn @click="darkMode">
          <v-icon class="mr-2"> mdi-brightness-4 </v-icon>
        </v-btn>
      </v-col>
      <v-col cols="5">
        <v-toolbar-title> Chacarecter Search</v-toolbar-title>
      </v-col>
      <v-col cols="4">
        <v-autocomplete
          :search-input.sync="searchInput"
          :items="items"
          dense
          filled
          label="Movie Name"
        ></v-autocomplete>
      </v-col>
      <v-col cols="2">
        <v-btn
          class="ml-2 mt-1 md1 ms-0"
          :disabled="!searchInput"
          color="error"
          @click="clear"
        >
          Clear
          <v-icon right> mdi-close-circle </v-icon>
        </v-btn>
      </v-col>
    </v-toolbar>

    <v-row>
      <v-template>
        <v-col class="container" cols="12">
          <v-card
            @click="openDialog(item._id)"
            elevation="12"
            class="cards"
            v-for="item in movieList"
            :key="item._id"
          >
            <!-- <v-img class="image-height" :src="`${item.Poster}`"> </v-img> -->
            <v-card-text>
              <v-row class="card-text mt-2">
                <strong> Name : </strong> {{ item.name }}</v-row
              >
              <v-row class="card-text mt-2">
                <strong> birth : </strong> {{ item.birth }}</v-row
              >
              <v-row class="card-text mt-2">
                <strong> death : </strong> {{ item.death }}</v-row
              >
              <v-row class="card-text mt-2">
                <strong> gender : </strong> {{ item.gender }}</v-row
              >
              <v-row class="card-text mt-2">
                <strong> hair : </strong> {{ item.hair }}</v-row
              >
              <v-row class="card-text mt-2">
                <strong> race : </strong> {{ item.race }}</v-row
              >
              <!-- <v-row class="card-text mt-6">
                <strong>Type : </strong> {{ item.Type }}</v-row
              >
              <v-row class="card-text mt-6">
                <strong>Year : </strong> {{ item.Year }}</v-row
              > -->
            </v-card-text>
          </v-card>
          <v-dialog v-model="dialog" scrollable max-width="80%">
            <v-card v-for="item in items" :key="item._id">
              <v-card-title>{{ item.name }}</v-card-title>
              <v-card-text height="200px">content</v-card-text>
              <v-btn color="error" @click="closeDialog"> Close</v-btn>
            </v-card>
          </v-dialog>
        </v-col>
      </v-template>
    </v-row>
  </div>
</template>

<script>
export default {
  name: "IndexPage",

  data() {
    return {
      dialog: false,
      loading: false,
      searchInput: "",
      movieList: [],
      items: [],
      descriptionLimit: 60,
      entries: [],
      isLoading: false,
      modal: false,
      search: null,
    };
  },

  mounted() {
    /* Theme starts with dark, user can change it. Movie search starts with "way" because empty displays is not seems good. First request for searching "way". */
    this.$vuetify.theme.dark = true;
    this.fetchMovie();
  },

  watch: {
    /* Want to watch movie list situtation for error handling. */
    movieList() {
      console.log("movieList", this.movieList);
    },

    /* movieList:{
      deep: true,
      handle() {
        console.log("asd", this.movieList);
      }
    }, */
    /* Watching searchInput model for searching dynamically, and add Titles in items array for autocompletion. Used then because request should finish before the push operation. */
    searchInput() {
      console.log("search", this.searchInput);
      if (this.searchInput == "") {
        this.fetchMovie();
      }
      let searchByFirstName = (str) =>
        this.movieList.filter(({ name }) => name.includes(str));
      console.log("ne bu", searchByFirstName(this.searchInput));
      this.movieList = searchByFirstName(this.searchInput);
    },
  },

  methods: {
    openDialog(id) {
      this.dialog = true;
      console.log(id);
      this.loading = true;
      const token = "gi_qbDO_T9tFDtkcIuOC";
      fetch(`https://the-one-api.dev/v2/character/` + id, {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      })
        .then((res) =>
          res
            .json()
            .then(
              (response) => (this.items = response.docs),
              (this.loading = false)
            )
        )
        .catch((err) => console.log("error", err));
    },

    closeDialog() {
      this.dialog = false;
      this.items = [];
    },
    /* theme selection*/
    darkMode() {
      this.$vuetify.theme.dark = !this.$vuetify.theme.dark;
    },
    /* Where we send a request and filling the movieList array. */
    async fetchMovie() {
      this.loading = true;
      const token = "gi_qbDO_T9tFDtkcIuOC";
      fetch(`https://the-one-api.dev/v2/character`, {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      })
        .then((res) =>
          res
            .json()
            .then(
              (response) => (this.movieList = response.docs),
              (this.loading = false)
            )
        )
        .catch((err) => console.log("error", err));
    },
    /* For better user exerience they can clear search input.*/
    clear() {
      this.movieList = [];
      this.searchInput = "";
    },
  },
};
</script>

<style>
.image-height {
  min-height: 400px;
  max-height: 400px;
}

.container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.cards {
  height: 300px;
  width: 400px;
  margin: 1%;
}

.card-text {
  text-transform: capitalize;
  font-size: 18px;
}

.container::after {
  display: flex;
  flex-wrap: wrap;
}

.row {
  display: block !important;
}
</style>
