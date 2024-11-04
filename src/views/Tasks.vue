<script setup lang="ts">
import TaskForm from '@/components/TaskForm.vue'
import { ref } from 'vue'
import { refAutoReset } from "@vueuse/core";

  const taskIcon = 'mdi-chevron-down-box'
  const errorIcon = 'mdi-radiobox-marked'
  const requirementIcon = 'mdi-checkbox-intermediate-variant'

  const search = ref('')
  const tasks = ref([])
  const taskSaved = refAutoReset(false, 3000)
  const dialog = ref(false)

  const headers = [
    { title: 'Тип', align: 'start', key: 'type' },
    { title: 'Номер', key: 'id', align: 'start' },
    { title: 'Тема', key: 'title', align: 'start' },
    { title: 'Статус', key: 'status', align: 'start' },
    { title: 'Исполнитель', key: 'executor', align: 'start' },
  ]

  const taskForm = ref();

  onMounted(async () => {
    const result = fetch('http://localhost:3000/tasks.json').then(res => {
      if (res.status === 200) {
        return res.json()
      }
    })
    result.then(res => {
      console.log('данные получены')
      tasks.value = res
    })
  })

  function onSubmit(item) {
    taskSaved.value = true
    tasks.value.forEach(task => {
      if (task.id === item.id) {
        task.title = item.title
        task.type = item.type
        task.status = item.status
        task.executor = item.executor
        task.image = item.image
        task.description = item.description
      }
    })
  }
</script>

<template>
  <div>
    <v-alert
      v-model="taskSaved"
      closable
      title="Задача обновлена"
      type="success"
      variant="flat"
    />
    <h1 class="pb-6">Задачи</h1>
    <v-sheet class="d-flex justify-space-between align-center pa-5">
      <div class="w-100 d-flex align-center">
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          class="pr-8"
          hide-details
          label="Поиск"
          max-width="500px"
          single-line
        />
        <v-dialog
          v-model="dialog"
          max-width="600"
        >
          <template v-slot:activator="{ props: activatorProps }">
            <v-btn
              class="text-none font-weight-regular"
              prepend-icon="mdi-filter"
              v-bind="activatorProps"
              rounded="xs"
              size="large"
              text="Фильтрация"
              variant="tonal"
            />
          </template>

          <v-card
            prepend-icon="mdi-filter"
            title="Фильтрация"
          >
            <v-card-text>
              <v-row dense>
                <v-col cols="6" sm="5">
                  <v-select
                    :items="['Задача', 'Ошибка', 'Требование']"
                    label="Тип"
                    required
                  />
                </v-col>
                <v-col cols="6" sm="7">
                  <v-text-field
                    hint="начните ввод наименования проекта, чтобы появились подсказки"
                    label="Проект"
                  />
                </v-col>
                <v-col cols="12" sm="5">
                  <v-select
                    :items="['Зарегистрирован', 'В работе', 'На проверке', 'Закрыт']"
                    label="Статус"
                    required
                  />
                </v-col>

                <v-col cols="6" sm="7">
                  <v-text-field
                    label="Исполнитель"
                    required
                  />
                </v-col>
              </v-row>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                text="Отмена"
                variant="plain"
                @click="dialog = false"
              />
              <v-btn
                color="green"
                text="Применить"
                variant="flat"
                @click="dialog = false"
              />
            </v-card-actions>
          </v-card>
        </v-dialog>
      </div>
      <v-dialog max-width="500">
        <template #activator="{ props: activatorProps }">
          <v-btn
            v-bind="activatorProps"
            color="green"
            flat
            rounded="xs"
            size="large"
          >
            Создать задачу
          </v-btn>
        </template>

        <template #default="{ isActive }">
          <v-card title="Создать задачу">
            <v-card-text>
              Тут надо добавить компонент формы
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                text="Отмена"
                @click="isActive.value = false"
              />
              <v-btn
                text="Сохранить"
                color="green"
                variant="flat"
                @click="isActive.value = false"
              />
            </v-card-actions>
          </v-card>
        </template>
      </v-dialog>
    </v-sheet>
    <v-data-table
      :headers="headers"
      :items="tasks"
      items-per-page-text="Строк"
      :search="search"
    >
      <template #item.type="{ item }">
        <v-icon :icon="item.type === 'Ошибка' ? errorIcon : item.type === 'Задача' ? taskIcon : requirementIcon" />
      </template>
      <template #item.id="{ item }">
        <v-dialog fullscreen="">
          <template #activator="{ props: activatorProps }">
            <span v-bind="activatorProps">{{ item.id }}</span>

          </template>

          <template #default="{ isActive }">
            <v-card :title="`Редактировать задачу ${item.id}`">
              <v-card-text>
                <TaskForm ref="taskForm" :task="item" @submit="isActive.value = false; onSubmit($event);" />
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                  text="Отмена"
                  @click="isActive.value = false"
                />
                <v-btn
                  color="green"
                  text="Сохранить"
                  variant="flat"
                  @click="taskForm.submit();"
                />
              </v-card-actions>
            </v-card>
          </template>
        </v-dialog>
      </template>
    </v-data-table>
  </div>
</template>
