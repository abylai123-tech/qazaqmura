<script>
import { useAPI } from '@/api'
import nocover from '@/assets/no-book-cover.svg'

export default {
  name: 'OnlineLibrary',
  data: () => {
    return {
      page: 1,
      length: 1,
      books: [],
      nocover: nocover
    }
  },
  methods: {
    async getBooks() {
      try {
        const api = useAPI()
        const response = await api.fetchData(`/v1/book?page=${this.page}&epub=1`)
        const { data, meta } = response.data
        this.books = data.items
        this.length = meta.last_page
      } catch (e) {
        console.error(e)
      }
    },
    getBookSubtitle(book) {
      let subtitle = book.book_author_main[0] ? book.book_author_main[0].name : ''
      return subtitle
    },
    getImageURL(url) {
      return import.meta.env.VITE_APP_API + '/storage/covers/' + url
    }
  },
  watch: {
    page() {
      this.getBooks()
    }
  },
  mounted() {
    this.getBooks()
  }
}
</script>

<template>
  <v-container class="h-100">
    <v-row>
      <v-col v-for="book in books" :key="book.id" cols="4">
        <v-card
          :title="book.title"
          :subtitle="getBookSubtitle(book)"
          class="h-100 d-flex flex-column"
        >
          <v-card-text>
            <v-row>
              <v-col cols="3">
                <v-img
                  :src="book.book_cover ? getImageURL(book.book_cover.value) : nocover"
                  height="200"
                  width="150"
                  class="rounded-xl"
                  fluid
                ></v-img>
              </v-col>
              <v-col>
                <span>{{ book.annotation }}</span>
              </v-col>
            </v-row>
          </v-card-text>

          <v-spacer></v-spacer>
          <v-card-actions>
            <v-btn :to="`/book/${book.id}`" variant="flat" class="mt-auto" color="primary"
              >Читать</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <v-pagination
          :total-visible="6"
          active-color="primary"
          variant="flat"
          v-model="page"
          :length="length"
        />
      </v-col>
    </v-row>
  </v-container>
</template>
