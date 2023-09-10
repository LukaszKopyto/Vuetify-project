<template>
  <v-layout class="rounded rounded-md">
    <v-app-bar color="blue-grey" density="compact">
      <template v-slot:prepend>
        <v-app-bar-nav-icon @click="drawer = !drawer" />
      </template>

      <v-app-bar-title> Company Employee List </v-app-bar-title>

      <template v-slot:append>
        <v-btn icon>
          <v-icon>mdi-magnify</v-icon>
        </v-btn>
      </template>
    </v-app-bar>

    <v-navigation-drawer v-model="drawer">
      <v-list>
        <v-list-item title="Menu"></v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-main class="main pa-8 mt-12">
      <router-view></router-view>
    </v-main>
  </v-layout>
</template>

<script setup lang="ts">
import { ref } from "vue";
import EmployeeTable from "@/components/EmployeeTable.vue";

const drawer = ref(false);
</script>

```
