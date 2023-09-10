<template>
  <v-snackbar
    v-model="isError"
    location="top"
    timeout="-1"
    color="yellow-darken-2"
    elevation="24"
  >
    <span class="pr-8">
      Something has gone wrong, please try again later.
      <strong> Error: {{ error }} </strong>
    </span>
  </v-snackbar>
  <v-progress-linear
    v-if="isFetching"
    indeterminate
    height="4"
    color="yellow-darken-2"
  ></v-progress-linear>
  <v-table v-if="result?.length" fixed-header :hover="true">
    <thead>
      <tr>
        <th
          v-for="column in columns"
          :key="column.value"
          class="bg-blue-grey-lighten-4 text-left pointer"
          @click="
            sortBy = column.value;
            order = order === 'asc' ? 'desc' : 'asc';
          "
        >
          <span
            :class="
              column.value === sortBy
                ? 'text-blue-grey-darken-4'
                : 'text-blue-grey'
            "
          >
            {{ column.text }}
          </span>
          <v-icon
            class="ml-2"
            :color="
              column.value === sortBy ? 'blue-grey' : 'blue-grey-lighten-3'
            "
            :icon="getIconName(column.value)"
            size="small"
            v-ripple
          ></v-icon>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in result" :key="item.id">
        <td>{{ item.first_name }}</td>
        <td>{{ item.last_name }}</td>
        <td>{{ item.email }}</td>
        <td>{{ item.phone }}</td>
        <td>{{ item.company }}</td>
        <td>{{ item.website }}</td>
      </tr>
    </tbody>
  </v-table>
  <v-pagination
    v-model="page"
    active-color="blue-grey-lighten-1"
    class="my-4"
    :length="length"
  ></v-pagination>
</template>

<script setup lang="ts">
import { computed, ref } from "vue";
import { useFetch } from "@vueuse/core";

type Order = "asc" | "desc";

const columns = ref([
  { text: "First name", value: "first_name" },
  { text: "Last name", value: "last_name" },
  { text: "Email", value: "email" },
  { text: "Phone", value: "phone" },
  { text: "Company", value: "company" },
  { text: "Website", value: "website" },
]);

const page = ref(1);
const sortBy = ref("last_name");
const order = ref<Order>("asc");

const isError = computed(() => !!error.value);
const url = computed(
  () =>
    `http://localhost:3001/people?_page=${page.value}&_limit=10&_sort=${sortBy.value}&_order=${order.value}`,
);

const result = computed(() => {
  return JSON.parse(data.value as string);
});
const length = computed(() => {
  if (!result.value) return 0;
  return result.value?.length ?? 0;
});

const { isFetching, error, data } = await useFetch(url, { refetch: true });

const getIconName = (value: string) => {
  if (value !== sortBy.value) {
    return "mdi-chevron-down";
  }
  if (order.value === "asc") {
    return "mdi-chevron-up";
  } else {
    return "mdi-chevron-down";
  }
};
</script>

<style scoped lang="scss">
.pointer {
  cursor: pointer;
}
</style>
```
