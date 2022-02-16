<template>
  <section>
    <div class="container">
      <b-loading
        :is-full-page="isFullPage"
        v-model="isLoading"
        :can-cancel="true"
      ></b-loading>
      <h1>Search Data Buku</h1>
      <div class="columns">
        <div class="column">
          <b-button
            class="btn-search"
            type="is-info is-light"
            @click="showWishlist"
            >Wishlist</b-button
          >
        </div>
        <div class="column is-four-fifths">
          <b-field label="Search">
            <b-input v-model="searchField" placeholder="Cari disini"></b-input>
          </b-field>
        </div>
        <div class="column">
          <b-button class="btn-search" type="is-danger" @click="search"
            >Search</b-button
          >
        </div>
      </div>
      <div v-if="wishlistShow" class="columns is-multiline">
        <h2>Wishlist Kamu</h2>
        <div v-for="book in wishlist" :key="book" class="column is-full">
          <div class="card">
            <div class="card-content">
              <div class="media">
                <div class="media-left">
                  <figure class="image is-48x48">
                    <img :src="book.url" alt="Placeholder image" />
                  </figure>
                </div>
                <div class="media-content">
                  <p class="title is-4">{{ book.title }}</p>
                  <div v-for="author in book.authors" :key="author">
                    <p class="subtitle is-6">@{{ author }}</p>
                  </div>
                </div>
              </div>

              <div class="content">
                <star-rating star-size="20" />
                <br />
                <div class="buttons">
                  <b-button
                    size="is-small"
                    type="is-warning"
                    @click="remove(book._id)"
                    >Remove From Favorite</b-button
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-if="searchShow" class="columns is-multiline">
        <h2>Wishlist Kamu</h2>
        <div v-for="book in books.items" :key="book" class="column is-full">
          <div class="card">
            <div class="card-content">
              <div class="media">
                <div class="media-left">
                  <figure class="image is-48x48">
                    <img
                      :src="book.volumeInfo.imageLinks.thumbnail"
                      alt="Placeholder image"
                    />
                  </figure>
                </div>
                <div class="media-content">
                  <p class="title is-4">{{ book.volumeInfo.title }}</p>
                  <div v-for="author in book.volumeInfo.authors" :key="author">
                    <p class="subtitle is-6">@{{ author }}</p>
                  </div>
                </div>
              </div>

              <div class="content">
                <star-rating star-size="20" />
                <br />
                <div class="buttons">
                  <b-button
                    size="is-small"
                    type="is-warning is-light"
                    @click="
                      add(
                        book.volumeInfo.imageLinks.thumbnail,
                        book.volumeInfo.title,
                        book.volumeInfo.authors
                      )
                    "
                    >Add To Favorite</b-button
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import axios from "axios";
import StarRating from "vue-star-rating";

export default {
  name: "Books",
  data() {
    return {
      books: [],
      wishlist: [],
      wishlistShow: true,
      searchShow: false,
      backendUrl: "http://localhost:3000/api/books",
      httpConfig: {
        timeout: 600000,
        headers: {
          Authorization: "Bearer jwtDummy",
        },
      },
      isLoading: false,
      isFullPage: true,
    };
  },
  components: {
    StarRating,
  },
  created() {
    this.isLoading = true;
    axios
      .get(`${this.backendUrl}/wishlist`, this.httpConfig)
      .then((response) => {
        this.wishlist = response.data.data;
        this.isLoading = false;
      })
      .catch((e) => {
        this.errors.push(e);
      });
  },
  methods: {
    search: function () {
      this.isLoading = true;
      axios
        .get(`${this.backendUrl}?q=${this.searchField}`, this.httpConfig)
        .then((response) => {
          this.books = response.data.data;
          this.wishlistShow = false;
          this.isLoading = false;
          this.searchShow = true;
        })
        .catch((e) => {
          this.errors.push(e);
        });
    },
    add: function (url, title, authors) {
      this.isLoading = true;
      const data = {
        url,
        title,
        authors,
      };
      axios
        .post(`${this.backendUrl}/wishlist`, data, this.httpConfig)
        .then(() => {
          this.$buefy.notification.open({
            message: "Added to Favorites!",
            type: "is-success",
          });
          this.isLoading = false;
        })
        .catch((e) => {
          this.errors.push(e);
        });
    },
    showWishlist: function () {
      this.isLoading = true;

      axios
        .get(`${this.backendUrl}/wishlist`, this.httpConfig)
        .then((response) => {
          this.wishlist = response.data.data;
          this.isLoading = false;
          this.wishlistShow = true;
          this.searchShow = false;
        })
        .catch((e) => {
          this.errors.push(e);
        });
    },
    remove: function (_id) {
      this.isLoading = true;
      axios
        .delete(`${this.backendUrl}/wishlist/${_id}`, this.httpConfig)
        .then(() => {
          axios
            .get(`${this.backendUrl}/wishlist`, this.httpConfig)
            .then((response) => {
              this.wishlist = response.data.data;
              this.isLoading = false;
              this.wishlistShow = true;
              this.searchShow = false;
              this.$buefy.notification.open({
                message: "Remove from Favorites!",
                type: "is-success",
              });
            })
            .catch((e) => {
              this.errors.push(e);
            });
        })
        .catch((e) => {
          this.errors.push(e);
        });
    },
  },
};
</script>

<style scoped>
.btn-search {
  margin-top: 32px;
}

h1 {
  text-align: center;
  font-size: 38px;
}

h2 {
  text-align: center;
  font-size: 24px;
  margin-top: 30px;
}

.title {
  margin-bottom: 5px !important;
}

.subtitle {
  font-size: 12px !important;
}

.content {
  margin-top: 30px;
}

.label {
  color: #ffffff !important;
}
</style>
