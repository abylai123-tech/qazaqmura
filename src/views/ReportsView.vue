<script setup lang="ts">
import { ref } from 'vue'
import { useI18n } from 'vue-i18n'
import { useAPI } from '@/api'
import fileDownload from 'js-file-download'
import report1 from '@/assets/reports/1.png'
import report2 from '@/assets/reports/2.png'
import report3 from '@/assets/reports/3.png'
import report4 from '@/assets/reports/4.png'
import report5 from '@/assets/reports/5.png'
import report6 from '@/assets/reports/6.png'
import report7 from '@/assets/reports/7.png'
import report8 from '@/assets/reports/8.png'
import report9 from '@/assets/reports/9.png'
import report10 from '@/assets/reports/10.png'
import report11 from '@/assets/reports/11.png'
import report12 from '@/assets/reports/12.png'
const { t } = useI18n()

const page = ref(1)
const length = ref(4)
const pageInput = ref(1)
const selectedImage = ref('')
const showImageDialog = ref(false)

const reports = ref([
  {
    id: 1,
    title: 'Фонд: Отчет по издателям',
    subtitle: 'desc',
    image: report1
  },
  {
    id: 2,
    title: 'Фонд: Список поступлений',
    subtitle: 'desc',
    image: report2
  },
  {
    id: 3,
    title: 'Фонд: Список фонда',
    subtitle: 'desc',
    image: report3
  },
  {
    id: 4,
    title: 'Фонд: Список основного фонда',
    subtitle: 'desc',
    image: report4
  },
  {
    id: 5,
    title: 'Фонд: Список учебников',
    subtitle: 'desc',
    image: report5
  },
  {
    id: 6,
    title: 'Контрагент',
    subtitle: 'desc',
    image: report6
  },
  {
    id: 7,
    title: 'Абонемент: Весь список',
    subtitle: 'desc',
    image: report7
  },
  {
    id: 8,
    title: 'Абонемент: Классный руководитель',
    subtitle: 'desc',
    image: report8
  },
  {
    id: 9,
    title: 'Абонемент: Школьники',
    subtitle: 'desc',
    image: report9
  },
  {
    id: 10,
    title: 'Абонемент: Сотрудники школы',
    subtitle: 'desc',
    image: report10
  },
  {
    id: 11,
    title: 'Инвентарный учет: Список инвентаризации',
    subtitle: 'desc',
    image: report11
  },
  {
    id: 12,
    title: 'Инвентарный учет: Список списанных книг',
    subtitle: 'desc',
    image: report12
  }
])

const api = useAPI()

function goToPage() {
  const pageNum = Number(pageInput.value)
  if (pageNum >= 1 && pageNum <= length.value) {
    page.value = pageNum
  } else {
    pageInput.value = page.value
  }
}

const downloadReport = async (type: 'excel' | 'pdf', reportId: number) => {
  try {
    if (reportId === 1) {
      const response = await api.postData(
        '/v1/book/school/publisher/report',
        { format: type },
        true
      )
      if (response.data) {
        if (type === 'excel') {
          console.log(response)

          fileDownload(response.data, `Фонд: Отчет по издателям.xlsx`)
        } else {
          console.log(response)

          fileDownload(response.data, `Фонд: Отчет по издателям.pdf`)
        }
      }
    } else if (reportId === 2) {
      const response = await api.postData(
        '/v1/book/school/admission/report',
        { format: type },
        true
      )
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Фонд: Список поступлений.xlsx`)
        } else {
          fileDownload(response.data, `Фонд: Список поступлений.pdf`)
        }
      }
    } else if (reportId === 3) {
      const response = await api.postData('/v1/book/school/book/report', { format: type }, true)
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Фонд: Список фонда.xlsx`)
        } else {
          fileDownload(response.data, `Фонд: Список фонда.pdf`)
        }
      }
    } else if (reportId === 4) {
      const response = await api.postData(
        '/v1/book/school/book/report?type_id=17',
        { format: type },
        true
      )
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Фонд: Список основного фонда.xlsx`)
        } else {
          fileDownload(response.data, `Фонд: Список основного фонда.pdf`)
        }
      }
    } else if (reportId === 5) {
      const response = await api.postData(
        '/v1/book/school/book/report?type_id=1',
        { format: type },
        true
      )
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Фонд: Список учебников.xlsx`)
        } else {
          fileDownload(response.data, `Фонд: Список учебников.pdf`)
        }
      }
    } else if (reportId === 6) {
      const response = await api.postData(
        '/v1/book/school/contractor/report',
        { format: type },
        true
      )
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Контрагент.xlsx`)
        } else {
          fileDownload(response.data, `Контрагент.pdf`)
        }
      }
    } else if (reportId === 7) {
      const response = await api.postData('/v1/user/user/report', { format: type }, true)
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Абонемент: Весь список.xlsx`)
        } else {
          fileDownload(response.data, `Абонемент: Весь список.pdf`)
        }
      }
    } else if (reportId === 8) {
      const response = await api.postData('/v1/user/user/report?role_id=4', { format: type }, true)
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Абонемент: Классный руководитель.xlsx`)
        } else {
          fileDownload(response.data, `Абонемент: Классный руководитель.pdf`)
        }
      }
    } else if (reportId === 9) {
      const response = await api.postData('/v1/user/user/report?role_id=5', { format: type }, true)
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Абонемент: Школьники.xlsx`)
        } else {
          fileDownload(response.data, `Абонемент: Школьники.pdf`)
        }
      }
    } else if (reportId === 10) {
      const response = await api.postData('/v1/user/user/report?role_id=6', { format: type }, true)
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Абонемент: Сотрудники школы.xlsx`)
        } else {
          fileDownload(response.data, `Абонемент: Сотрудники школы.pdf`)
        }
      }
    } else if (reportId === 11) {
      const response = await api.postData(
        '/v1/book/school/inventory/report',
        { format: type },
        true
      )
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Инвентарный учет: Список инвентаризации.xlsx`)
        } else {
          fileDownload(response.data, `Инвентарный учет: Список инвентаризации.pdf`)
        }
      }
    } else if (reportId === 12) {
      const response = await api.postData(
        '/v1/book/school/inventory/decommissioned/report',
        { format: type },
        true
      )
      if (response.data) {
        if (type === 'excel') {
          fileDownload(response.data, `Инвентарный учет: Список списанных книг.xlsx`)
        } else {
          fileDownload(response.data, `Инвентарный учет: Список списанных книг.pdf`)
        }
      }
    }
  } catch (error) {
    console.error('Error downloading report:', error)
  }
}

const openImageDialog = (image: string) => {
  selectedImage.value = image
  showImageDialog.value = true
}
</script>

<template>
  <v-container class="pa-0" fluid>
    <!-- Image Dialog -->
    <v-dialog v-model="showImageDialog" max-width="800">
      <v-card>
        <v-card-text class="pa-0">
          <v-img :src="selectedImage" contain height="80vh" class="bg-grey-lighten-2"></v-img>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" variant="text" @click="showImageDialog = false"> Закрыть </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-app-bar class="px-4 mb-4" color="white" flat>
      <template v-slot:title>
        <div class="d-flex flex-column">
          <span class="text-h6 font-weight-bold">Генерация документов</span>
          <span class="text-subtitle-2 text-medium-emphasis"
            >Генерация электронных отчетов по фонду</span
          >
        </div>
      </template>

      <template v-slot:append>
        <v-btn class="ml-2" color="primary" prepend-icon="mdi-help-circle-outline" variant="flat">
          Помощь
        </v-btn>
      </template>
    </v-app-bar>

    <v-card class="mx-4 my-4" flat>
      <v-card-text>
        <v-row>
          <v-col v-for="report in reports" :key="report.id" cols="12" md="6">
            <v-sheet class="d-flex my-2" height="120">
              <v-img
                class="bg-grey-lighten-2 rounded-lg cursor-pointer"
                width="160"
                height="120"
                cover
                style="min-width: 160px; max-width: 160px"
                :src="report.image"
                @click="openImageDialog(report.image)"
              ></v-img>
              <div class="d-flex flex-column flex-grow-1 justify-space-between pl-4">
                <div class="mb-auto">
                  <div class="text-h6 font-weight-bold">{{ report.title }}</div>
                  <div class="text-subtitle-2 text-grey mt-1">{{ report.subtitle }}</div>
                </div>
                <div class="d-flex gap-2">
                  <v-btn
                    density="comfortable"
                    prepend-icon="mdi-microsoft-excel"
                    variant="flat"
                    class="bg-grey-lighten-4 text-capitalize"
                    @click="downloadReport('excel', report.id)"
                  >
                    Скачать Excel
                  </v-btn>
                  <v-btn
                    density="comfortable"
                    prepend-icon="mdi-file-pdf-box"
                    variant="flat"
                    class="bg-grey-lighten-4 text-capitalize"
                    @click="downloadReport('pdf', report.id)"
                  >
                    Скачать PDF
                  </v-btn>
                </div>
              </div>
            </v-sheet>
          </v-col>
        </v-row>

        <!-- <v-row class="mt-4">
          <v-pagination
            v-model="page"
            :length="length"
            :total-visible="6"
            active-color="primary"
            class="ml-auto"
            size="small"
            variant="flat"
          ></v-pagination>

          <div class="d-flex align-center mr-2">
            <v-text-field
              v-model="pageInput"
              :min="1"
              :max="length"
              density="compact"
              hide-details
              class="mx-2"
              style="width: 120px"
              variant="outlined"
            >
              <template v-slot:append>
                <v-tooltip
                  :model-value="pageInput < 1 || pageInput > length"
                  location="bottom"
                  :text="`Введите число от 1 до ${length}`"
                >
                  <template v-slot:activator="{ props }">
                    <v-icon
                      v-bind="props"
                      color="error"
                      icon="mdi-alert-circle"
                      v-show="pageInput < 1 || pageInput > length"
                    ></v-icon>
                  </template>
                </v-tooltip>
              </template>
            </v-text-field>
            <v-btn
              icon="mdi-magnify"
              size="small"
              variant="flat"
              @click="goToPage"
              class="rounded-0"
            ></v-btn>
          </div>
        </v-row> -->
      </v-card-text>
    </v-card>
  </v-container>
</template>

<style scoped>
.gap-2 {
  gap: 16px;
}

.cursor-pointer {
  cursor: pointer;
}
</style>
