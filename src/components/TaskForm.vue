<script setup lang="ts">
  import { ref } from 'vue'

  const props = defineProps<{
    task: Record<string, any>;
}>()

  const emit = defineEmits(["submit"])

  const executors = ['Попов Виталий', 'Симонова Екатерина', 'Бородин Игорь', 'не назначен']

  const valid = ref(true)
  const form = ref({
    image: [],
    description: '',
    ...props.task,
  })

  function handleSubmit () {
    if (!valid.value) return
    emit('submit', form.value)
  }
  const submitBtn = ref();
  const submit = () => submitBtn.value.click()

  defineExpose({ submit })

  function isRequired (value: string) {
    if (value) return true
    return 'Поле обязательно для заполнения'
  }

  function isNotEmpty (value: string[]) {
    if (value && value.length) return true
    return 'Выберите одну опцию'
  }
</script>

<template>
  <div>
    <v-form v-model="valid" @submit.prevent="handleSubmit">
      <v-text-field v-model="form.title" label="Тема" :rules="[isRequired]" />
      <v-row>
        <v-col cols="6">
          <v-combobox
            v-model="form.type"
            chips
            :items="['Задача', 'Ошибка', 'Требование']"
            label="Тип"
            :rules="[isNotEmpty]"
          />
        </v-col>
        <v-col cols="6">
          <v-select
            v-model="form.executor"
            :items="executors"
            label="Исполнитель"
            :rules="[isNotEmpty]"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="6">
          <v-textarea v-model="form.description" label="Описание" />
        </v-col>
        <v-col cols="6">
          <v-file-input
            v-model="form.image"
            accept="image/png, image/jpeg, image/bmp"
            label="Загрузите изображение"
            placeholder="Загрузите изображение"
            prepend-icon="mdi-camera"
          />
        </v-col>
      </v-row>
      <button ref="submitBtn" class="d-none" type="submit">Submit</button>
    </v-form>
  </div>
</template>

<style lang="scss" scoped>

</style>
