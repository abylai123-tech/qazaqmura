<script lang="ts" setup>
import { ref, type Ref } from 'vue'
import { useAPI } from '@/api'
import { useRouter } from 'vue-router'
import { useI18n } from 'vue-i18n'
import { useToastStore } from '@/stores/toast'

const { t } = useI18n()
const api = useAPI()
const toast = useToastStore()
const router = useRouter()

interface Form {
  title: string
  author_id: number[]
  author_id_main: number[]
  language_id: number[]
  link: { URL: string; title: string }
  materials: number[]
  type_id: number | null
  year: string
}

const form: Ref<Form> = ref({
  title: '',
  author_id: [],
  author_id_main: [],
  language_id: [],
  link: { URL: '', title: '' },
  materials: [],
  type_id: null,
  year: ''
})

const authors: Ref<{ id: number; name: string }[]> = ref([])
const types: Ref<{ id: number; title: string }[]> = ref([])
const languages: Ref<{ id: number; title: string }[]> = ref([])
const materials: Ref<{ id: number; label: string }[]> = ref([])

const cover = ref<HTMLInputElement | null>(null)
const epub = ref<HTMLInputElement | null>(null)
const coverPreview = ref('')
const epubFile = ref<File | null>(null)

const titleValid = ref(false)
const authorMainValid = ref(false)
const epubValid = ref(false)
const coverValid = ref(false)

const titleError = ref(false)
const authorMainError = ref(false)
const epubError = ref(false)
const coverError = ref(false)

const formSubmitted = ref(false)

const rules = {
  required: (value: any) => !!value || 'Поле обязательно для заполнения'
}

async function getAuthors(search = '') {
  try {
    const response = await api.fetchData<{ data: { items: { id: number; name: string }[] } }>(
      `/v1/author?search=${search}`
    )
    if (response.data) authors.value = response.data.data.items
  } catch (error: any) {
    toast.error('Ошибка при загрузке списка авторов')
  }
}

async function getTypes(search = null) {
  try {
    let request = `/v1/type`
    if (search) {
      request += `?search=${search}`
    }
    const response = await api.fetchData<{ data: { items: { id: number; title: string }[] } }>(
      request
    )
    if (response.data) types.value = response.data.data.items
  } catch (error: any) {
    console.error('Error:', error)
  }
}

async function getLanguages(search = null) {
  try {
    let request = `/v1/language`
    if (search) {
      request += `?search=${search}`
    }
    const response = await api.fetchData<{ data: { items: { id: number; title: string }[] } }>(
      request
    )
    if (response.data) languages.value = response.data.data.items
  } catch (error: any) {
    console.error('Error:', error)
  }
}

async function getMaterials(search = null) {
  try {
    let request = `/v1/material`
    if (search) {
      request += `?search=${search}`
    }
    const response = await api.fetchData<{ data: { items: { id: number; label: string }[] } }>(
      request
    )
    if (response.data) materials.value = response.data.data.items
  } catch (error: any) {
    console.error('Error:', error)
  }
}

function handleUpload() {
  cover.value?.click()
}

function handleEpub() {
  epub.value?.click()
}

function handleFile(event: Event) {
  const target = event.target as HTMLInputElement
  if (target.files && target.files[0]) {
    const file = target.files[0]
    coverPreview.value = URL.createObjectURL(file)
    coverValid.value = true
    coverError.value = false
  }
}

function handleEpubUpload(event: Event) {
  const target = event.target as HTMLInputElement
  if (target.files && target.files[0]) {
    epubFile.value = target.files[0]
    epubValid.value = true
    epubError.value = false
  }
}

async function sendBookData() {
  formSubmitted.value = true

  titleValid.value = !!form.value.title
  authorMainValid.value = form.value.author_id_main.length > 0
  epubValid.value = !!epubFile.value
  coverValid.value = !!coverPreview.value

  titleError.value = !titleValid.value
  authorMainError.value = !authorMainValid.value
  epubError.value = !epubValid.value
  coverError.value = !coverValid.value

  const isValid = titleValid.value && authorMainValid.value && epubValid.value && coverValid.value

  if (!isValid) {
    toast.error('Пожалуйста, заполните все обязательные поля')
    return
  }

  try {
    const response = await api.postData('/v1/book', form.value)
    const bookId = response.data.id

    if (cover.value?.files?.[0]) {
      const coverFormData = new FormData()
      coverFormData.append('cover', cover.value.files[0])
      coverFormData.append('book_id', bookId.toString())
      await api.postData('/v1/book/cover', coverFormData)
    }

    if (epubFile.value) {
      const epubFormData = new FormData()
      epubFormData.append('epub', epubFile.value)
      epubFormData.append('book_id', bookId.toString())
      await api.postData('/v1/book/epub', epubFormData)
    }

    toast.success('Запись успешно добавлена')
  } catch (error: any) {
    let errorMessage = error?.message || 'Ошибка при добавлении книги'
    toast.error(errorMessage)
    console.error('Error:', error)
  } finally {
    router.push('/m-data')
  }
}

const handleMainAuthorSelect = async (value: number) => {
  if (value) {
    form.value.author_id_main = [value]
    authorMainValid.value = true
    authorMainError.value = false
    await getAuthors('')
  }
}

const handleAdditionalAuthorSelect = async (value: number[]) => {
  if (value) {
    form.value.author_id = value
    await getAuthors('')
  }
}

const handleMaterialSelect = (value: number) => {
  if (value) {
    form.value.materials = [value]
  }
}

getAuthors()
getTypes()
getLanguages()
getMaterials()
</script>

<template>
  <v-container fluid>
    <v-app-bar>
      <template v-slot:title>
        <div class="d-flex flex-column">
          <span class="text-h6 font-weight-bold">M-DATA</span>
          <span class="text-subtitle-2 text-medium-emphasis">{{ t('database_by_rk') }}</span>
        </div>
      </template>
    </v-app-bar>

    <v-row>
      <v-col>
        <v-btn prepend-icon="mdi-arrow-left" to="/m-data" variant="text">Назад</v-btn>
      </v-col>
    </v-row>

    <v-row class="mt-6">
      <v-col cols="8" offset="2">
        <v-card>
          <v-card-title>Основные данные</v-card-title>
          <v-card-text>
            <v-form>
              <v-container fluid>
                <v-row>
                  <v-col>
                    <v-text-field
                      v-model="form.title"
                      :label="t('name') + ' *'"
                      placeholder="Напишите название"
                      :rules="[rules.required]"
                      :error="titleError"
                      @update:model-value="titleValid = $event?.length > 0"
                      required
                      variant="outlined"
                    ></v-text-field>
                  </v-col>
                </v-row>

                <v-row>
                  <v-col>
                    <v-autocomplete
                      v-model="form.author_id_main[0]"
                      :items="authors"
                      item-title="name"
                      item-value="id"
                      :label="t('author') + ' *'"
                      :rules="[rules.required]"
                      :error="authorMainError"
                      @update:model-value="handleMainAuthorSelect"
                      @update:search="getAuthors"
                      variant="outlined"
                    ></v-autocomplete>
                  </v-col>
                </v-row>

                <v-row>
                  <v-col>
                    <v-autocomplete
                      v-model="form.author_id"
                      :items="authors"
                      item-title="name"
                      item-value="id"
                      label="Другие авторы"
                      multiple
                      @update:model-value="handleAdditionalAuthorSelect"
                      @update:search="getAuthors"
                      variant="outlined"
                    ></v-autocomplete>
                  </v-col>
                </v-row>

                <v-row>
                  <v-col>
                    <v-autocomplete
                      v-model="form.type_id"
                      :items="types"
                      item-title="title"
                      item-value="id"
                      label="Тип"
                      variant="outlined"
                    ></v-autocomplete>
                  </v-col>
                </v-row>

                <v-row>
                  <v-col>
                    <v-autocomplete
                      v-model="form.language_id"
                      :items="languages"
                      item-title="title"
                      item-value="id"
                      label="Язык"
                      multiple
                      variant="outlined"
                    ></v-autocomplete>
                  </v-col>
                </v-row>

                <v-row>
                  <v-col>
                    <v-text-field
                      v-model="form.year"
                      :label="t('year_of_publication')"
                      placeholder="Укажите"
                      type="number"
                      variant="outlined"
                    ></v-text-field>
                  </v-col>
                </v-row>

                <v-row>
                  <v-col>
                    <v-autocomplete
                      v-model="form.materials[0]"
                      :items="materials"
                      item-title="label"
                      item-value="id"
                      label="Обозначение материала"
                      @update:model-value="handleMaterialSelect"
                      variant="outlined"
                    ></v-autocomplete>
                  </v-col>
                </v-row>

                <v-row>
                  <v-col>
                    <div class="d-flex justify-space-between">
                      <div class="d-flex flex-column">
                        <span class="mb-2">Обложка *</span>
                        <v-img v-if="coverPreview" :src="coverPreview"></v-img>
                        <v-btn
                          color="primary"
                          variant="outlined"
                          @click="handleUpload"
                          :error="coverError"
                        >
                          Выбрать файл
                        </v-btn>
                        <input
                          ref="cover"
                          accept="image/png, image/jpg, image/jpeg"
                          style="display: none"
                          type="file"
                          @input="handleFile"
                        />
                        <small>до 5 MB в соотношении 70×100, формата png, jpg, jpeg</small>
                      </div>
                      <div class="d-flex flex-column ml-4">
                        <span class="mb-2">EPUB *</span>
                        <v-btn
                          color="primary"
                          variant="outlined"
                          @click="handleEpub"
                          :error="epubError"
                        >
                          Выбрать файл
                        </v-btn>
                        <input
                          ref="epub"
                          accept=".epub"
                          style="display: none"
                          type="file"
                          @input="handleEpubUpload"
                        />
                        <div v-if="epubFile" class="mt-2">
                          <div class="text-body-2">{{ epubFile.name }}</div>
                          <div class="text-caption">
                            {{ (epubFile.size / (1024 * 1024)).toFixed(2) }} MB
                          </div>
                        </div>
                      </div>
                    </div>
                  </v-col>
                </v-row>
              </v-container>
            </v-form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <v-row>
      <v-col class="text-center">
        <v-btn color="primary" prepend-icon="mdi-plus" variant="flat" @click="sendBookData"
          >Добавить запись
        </v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>
.error-text {
  color: rgb(var(--v-theme-error)) !important;
}
</style>
