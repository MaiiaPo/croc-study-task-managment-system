<script setup>
  import { ref } from 'vue'
  const drawer = ref(false)

</script>

<template>
  <v-responsive class="border rounded">
    <v-app>
      <v-app-bar>
        <template #prepend>
          <v-app-bar-nav-icon
            variant="text"
            @click.stop="drawer = !drawer"
          />
          <v-toolbar-title>
            <router-link to="/" class="text-decoration-none title-link">
              FASTER TASK
            </router-link>
          </v-toolbar-title>
        </template>
        <template #append>
          <v-menu>
            <template v-slot:activator="{ props }">
              <v-avatar v-bind="props" size="50" color="green">
                <span>MP</span>
              </v-avatar>
            </template>
            <v-list>
              <v-list-item
                v-for="(item, index) in ['Профиль', 'Настройки', 'Выйти']"
                :key="index"
                :value="index"
              >
                <v-list-item-title>{{ item }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>
        </template>
      </v-app-bar>

      <v-navigation-drawer
        v-model="drawer"
        expand-on-hover
        rail
      >
        <v-list density="compact" nav>
          <v-list-item
            prepend-icon="mdi-folder"
            title="Проекты"
            :to="`/projects`"
            value="projects"
          />
          <v-list-item
            prepend-icon="mdi-file-document-multiple-outline"
            title="Задачи"
            :to="`/tasks`"
            value="tasks"
          />
        </v-list>
      </v-navigation-drawer>

      <v-main>
        <div class="pa-5">
          <router-view></router-view>
        </div>
      </v-main>
    </v-app>
  </v-responsive>
</template>

<style lang="scss" scoped>
.title-link {
  color: lightgreen;
}

</style>
