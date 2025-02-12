<script setup>
import { useAPI } from '@/api'
import nocover from '@/assets/no-book-cover.svg'
import { useI18n } from 'vue-i18n'
import { ref, onMounted, watch } from 'vue'

const { t } = useI18n()
const api = useAPI()

const page = ref(1)
const length = ref(1)
const books = ref([])
const search = ref('')
const loading = ref(false)
const searchQuery = ref('')

async function getBooks() {
  loading.value = true
  try {
    let url = `/v1/book?page=${page.value}&epub=1`
    if (searchQuery.value) {
      url += `&title=${searchQuery.value}`
    }
    const response = await api.fetchData(url)
    const { data, meta } = response.data
    books.value = data.items
    length.value = meta.last_page
  } catch (e) {
    console.error(e)
  } finally {
    loading.value = false
  }
}

function handleSearch() {
  searchQuery.value = search.value
  page.value = 1
  getBooks()
}

function handleClear() {
  search.value = ''
  searchQuery.value = ''
  page.value = 1
  getBooks()
}

function handleKeydown(event) {
  if (event.key === 'Enter') {
    handleSearch()
  }
}

function getBookSubtitle(book) {
  let subtitle = book.book_author_main[0] ? book.book_author_main[0].name : ''
  return subtitle
}

function getImageURL(url) {
  return import.meta.env.VITE_APP_API + '/storage/covers/' + url
}

watch(page, () => {
  getBooks()
})

onMounted(() => {
  getBooks()
})
</script>

<template>
  <v-container class="h-100">
    <v-row>
      <v-col cols="12">
        <v-card class="rounded-lg" elevation="1">
          <v-card-text>
            <v-row align="center">
              <v-col cols="8">
                <v-text-field
                  v-model="search"
                  :loading="loading"
                  clearable
                  hide-details
                  :placeholder="'Поиск по названию'"
                  prepend-inner-icon="mdi-magnify"
                  single-line
                  variant="outlined"
                  density="compact"
                  bg-color="white"
                  @keydown="handleKeydown"
                ></v-text-field>
              </v-col>
              <v-col cols="2" class="d-flex search-buttons">
                <v-btn
                  color="primary"
                  :loading="loading"
                  variant="elevated"
                  elevation="1"
                  @click="handleSearch"
                >
                  Искать
                </v-btn>
                <v-btn variant="elevated" elevation="1" @click="handleClear"> Сбросить </v-btn>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col v-for="book in books" :key="book.id" cols="12" md="6">
        <v-card class="pa-4 h-100 custom-border" elevation="0" variant="outlined">
          <v-row>
            <v-col cols="auto">
              <v-img
                :src="book.book_cover ? getImageURL(book.book_cover.value) : nocover"
                height="150"
                width="100"
                class="rounded"
                cover
              ></v-img>
            </v-col>
            <v-col class="flex-grow-1">
              <div class="d-flex flex-column justify-space-between h-100">
                <div>
                  <h3 class="mb-1">{{ book.title }}</h3>
                  <div class="text-primary mb-4">{{ getBookSubtitle(book) }}</div>
                </div>
                <div class="d-flex flex-wrap align-center metadata-info">
                  <div class="text-caption text-grey">
                    Год издания: {{ book.year || 'Не указан' }}
                  </div>
                  <v-chip
                    color="error"
                    size="small"
                    variant="flat"
                    class="text-uppercase"
                    density="compact"
                  >
                    EPUB
                  </v-chip>
                  <!-- <div class="text-caption text-grey">Тип: {{ book.type?.title || 'Скоро' }}</div> -->
                </div>
                <div class="d-flex w-100">
                  <v-btn
                    :href="`/book/${book.id}`"
                    target="_blank"
                    color="primary"
                    variant="elevated"
                    class="px-6 w-100"
                    size="small"
                  >
                    Читать
                  </v-btn>
                </div>
              </div>
            </v-col>
          </v-row>
        </v-card>
      </v-col>
    </v-row>
    <v-row v-if="books.length === 0 && !loading">
      <v-col class="text-center">
        <span class="text-medium-emphasis">{{ t('no_data') }}</span>
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

<style scoped>
.custom-border {
  border-color: #a0a0a0 !important;
}

.search-buttons {
  gap: 8px;
}

.metadata-info {
  gap: 8px;
}

@media not all and (min-resolution:.001dpcm) { 
  @supports (-webkit-appearance:none) {
    h3 {
      font-weight: bold;
      color: black;
    }
  }
}
@supports (-webkit-touch-callout: none) {
  h3 {
    color: black;
    font-weight: bold;
  }
}

</style>
