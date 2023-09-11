<template>
  <Suspense>
    <EmployeeTable />
    <template #fallback>
      <v-progress-linear
        indeterminate
        height="4"
        color="yellow-darken-2"
      ></v-progress-linear>
    </template>
  </Suspense>
</template>

<script lang="ts" setup>
import EmployeeTable from "@/components/EmployeeTable.vue";
</script>
