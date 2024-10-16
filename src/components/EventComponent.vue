<script>
import { DateTime } from 'luxon'
import { useAPI } from '@/api'
import { useAuth } from '@/auth/index'

export default {
  components: {},
  data: () => ({
    tab: 0,
    dates: [],
    events: [],
    newEvent: {
      title: '',
      description: '',
      date: '',
      time: ''
    }
  }),
  mounted() {
    this.getEvents()
  },
  methods: {
    async createEvent() {
      try {
        const api = useAPI()
        const auth = useAuth()
        console.log(auth.userData.school)
        this.newEvent.school_id = auth.userData.value.school.id
        await api.postData('/v1/event', this.newEvent)
        this.newEvent = {
          title: '',
          description: '',
          date: '',
          time: ''
        }
        this.getEvents()
      } catch (e) {
        console.error(e)
      }
    },
    async getEvents() {
      try {
        const api = useAPI()
        const response = await api.fetchData('/v1/event')
        this.events = response.data.data.items
        this.dates = this.events.map((event) => {
          const dateTimeString = `${event.date}T${event.time}:00`
          return DateTime.fromISO(dateTimeString)
        })
        console.log(this.events)
      } catch (e) {
        console.error(e)
      }
    }
  }
}
</script>

<template>
  <v-card>
    <v-card-title>Мероприятия</v-card-title>
    <v-card-text>
      <v-tabs v-model="tab" color="primary">
        <v-tab :value="0">Основное</v-tab>
      </v-tabs>

      <v-tabs-window v-model="tab">
        <v-tabs-window-item :value="0">
          <v-container fluid>
            <v-row>
              <v-col>
                <v-date-picker height="430" hide-header multiple v-model="dates" color="primary" />
              </v-col>
              <v-col>
                <v-list class="overflow-auto" height="430" color="primary" lines="two">
                  <v-list-item
                    v-for="event in events"
                    class="mb-4"
                    :key="event.id"
                    :value="event"
                    rounded="xl"
                    :title="event.title"
                  >
                    <v-list-item-subtitle>
                      <div>{{ event.date }} - {{ event.time }}</div>
                      <div>{{ event.description }}</div>
                    </v-list-item-subtitle>
                  </v-list-item>
                </v-list>
              </v-col>
              <v-col>
                <v-card>
                  <v-card-title>Добавление мероприятия</v-card-title>
                  <v-card-text>
                    <v-form>
                      <v-text-field
                        v-model="newEvent.title"
                        density="compact"
                        variant="outlined"
                        hide-details
                        label="Заголовок"
                      />
                      <v-textarea
                        v-model="newEvent.description"
                        density="compact"
                        class="my-3"
                        variant="outlined"
                        hide-details
                        label="Описание"
                      />
                      <v-text-field
                        v-model="newEvent.date"
                        class="mb-3"
                        type="date"
                        density="compact"
                        variant="outlined"
                        hide-details
                        label="Дата"
                      />
                      <v-text-field
                        v-model="newEvent.time"
                        density="compact"
                        type="time"
                        variant="outlined"
                        hide-details
                        label="Время"
                      />
                    </v-form>
                  </v-card-text>
                  <v-card-actions>
                    <v-btn color="primary" @click="createEvent">Добавить</v-btn>
                  </v-card-actions>
                </v-card>
              </v-col>
            </v-row>
          </v-container>
        </v-tabs-window-item>
        <v-tabs-window-item :value="1"></v-tabs-window-item>
      </v-tabs-window>
    </v-card-text>
  </v-card>
</template>
