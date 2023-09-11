<template>
  <v-snackbar
    v-model="isError"
    location="top"
    timeout="-1"
    color="yellow-darken-2"
    elevation="24"
  >
    <span class="pr-8">
      {{ error }}
    </span>
  </v-snackbar>
  <v-progress-linear
    v-show="isFetching"
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
        <td class="d-flex align-center justify-space-between">
          <span>{{ item.website }}</span>
          <v-btn
            color="blue-grey"
            class="pl-auto"
            @click="(isOpen = true), (idToRemove = item.id)"
          >
            Delete
          </v-btn>
        </td>
      </tr>
    </tbody>
  </v-table>
  <v-pagination
    v-if="result?.length"
    v-model="page"
    active-color="blue-grey-lighten-1"
    class="my-4"
    :length="length"
  ></v-pagination>
  <v-dialog v-model="isOpen" width="auto">
    <v-card>
      <v-card-text>
        Are you sure you want to remove an employee from the list?
      </v-card-text>
      <v-divider class="my-3" />
      <v-card-actions>
        <v-btn class="flex-grow-1" variant="tonal" @click="isOpen = false">
          Close Dialog
        </v-btn>
        <v-btn
          color="yellow-darken-2"
          class="flex-grow-1"
          variant="flat"
          :loading="loading"
          @click="removeEmployee"
        >
          Remove
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script setup lang="ts">
import { computed, ref } from "vue";
import { useFetch } from "@vueuse/core";

type Order = "asc" | "desc";

type SortBy =
  | "first_name"
  | "last_name"
  | "email"
  | "phone"
  | "company"
  | "website";

type Person = {
  id: number;
  first_name: string;
  last_name: string;
  email: string;
  phone: string;
  company: string;
  website: string;
};

type Column = {
  text: string;
  value: SortBy;
};

const columns: Column[] = [
  { text: "First name", value: "first_name" },
  { text: "Last name", value: "last_name" },
  { text: "Email", value: "email" },
  { text: "Phone", value: "phone" },
  { text: "Company", value: "company" },
  { text: "Website", value: "website" },
];

const API_URL = import.meta.env?.VITE_API_BASE ?? "http://localhost:3001/people";

const page = ref(1);
const length = ref(0);
const sortBy = ref<SortBy>("last_name");
const order = ref<Order>("asc");
const isOpen = ref(false);
const idToRemove = ref<number | null>(null);
const loading = ref(false);

const isError = computed(() => !!error.value);
const url = computed(() => {
  return `${API_URL}?_page=${page.value}&_limit=10&_sort=${sortBy.value}&_order=${order.value}`;
});

const result = computed<Person[]>(() => {
  if (!data.value) return [];
  return data.value;
});

const { error, data, isFetching, response, execute } = await useFetch<Person[]>(
  url,
  {
    refetch: true,
  },
).json();

if (!length.value) {
  const num = Number(response.value?.headers?.get("x-total-count") ?? 0);
  if (typeof num === "number") {
    length.value = Math.floor(num / 10);
  }
}

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

const removeEmployee = async () => {
  loading.value = true;
  await useFetch(`${API_URL}/${idToRemove.value}`, {
    method: "DELETE",
  });
  loading.value = false;
  isOpen.value = false;
  await execute();
};
</script>

<style scoped lang="scss">
.pointer {
  cursor: pointer;
}
</style>
```
