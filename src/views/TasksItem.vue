<script setup lang="ts">
  import { useRoute } from 'vue-router'
  let currentTask = ref({})

  const route = useRoute()

  onMounted(async () => {
    const currentId = route.params.id;
    const result = fetch('http://localhost:3000/tasks.json').then(res => {
      if (res.status === 200) {
        return res.json()
      }
    })

    result.then(res => {
      currentTask.value = res.filter(task => task.id === currentId)[0];
    })
  })
</script>

<template>
  <div>
    <h1 v-if="currentTask" class="pb-6">Задача {{ currentTask.id }} </h1>
  </div>
</template>
