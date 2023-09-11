<template>
  <form @submit.prevent="submit">
    <v-text-field
      v-model="firstName"
      :counter="10"
      :error-messages="firstNameErrorMessage"
      label="First Name"
    ></v-text-field>
    <v-text-field
      v-model="lastName"
      :counter="10"
      :error-messages="lastNameErrorMessage"
      label="Last Name"
    ></v-text-field>
    <v-text-field
      v-model="phone"
      :counter="7"
      :error-messages="phoneErrorMessage"
      label="Phone Number"
    ></v-text-field>
    <v-text-field
      v-model="email"
      :error-messages="emailErrorMessage"
      label="E-mail"
      type="email"
    ></v-text-field>
    <v-text-field
      v-model="company"
      :error-messages="companyErrorMessage"
      label="Company"
    ></v-text-field>
    <v-text-field
      v-model="website"
      :error-messages="websiteErrorMessage"
      label="Website URL"
    ></v-text-field>
    <v-btn
      color="yellow-darken-2"
      :loading="loading"
      class="me-4"
      type="submit"
    >
      submit
    </v-btn>
    <v-btn color="blue-grey" @click="handleReset"> clear </v-btn>
  </form>
  <v-snackbar
    v-model="showSnackbar"
    class="pointer"
    location="top"
    timeout="10000"
    color="yellow-darken-2"
    elevation="24"
    @click="showSnackbar = false"
  >
    <span class="pr-8">New employee added</span>
  </v-snackbar>
</template>

<script setup lang="ts">
import { useField, useForm } from "vee-validate";
import { toTypedSchema } from "@vee-validate/zod";
import * as zod from "zod";
import { useFetch } from "@vueuse/core/index";
import { ref } from "vue";

const API_URL =
  import.meta.env?.VITE_API_BASE ?? "http://localhost:3001/people";

const loading = ref(false);
const showSnackbar = ref(false);

const validationSchema = toTypedSchema(
  zod.object({
    first_name: zod
      .string()
      .nonempty("This is required")
      .min(3, { message: "First name needs to be at least 3 characters." }),
    last_name: zod
      .string()
      .nonempty("This is required")
      .min(3, { message: "Last name needs to be at least 3 characters." }),
    email: zod
      .string()
      .nonempty("This is required")
      .email({ message: "Must be a valid email" }),
    phone: zod
      .string()
      .nonempty("This is required")
      .min(9, { message: "Phone number needs to be at least 9 digits." }),
    company: zod.string().nonempty("This is required"),
    website: zod
      .string()
      .nonempty("This is required")
      .trim()
      .startsWith("http", "Website url must start with http")
      .url("Must be a valid url"),
  }),
);

const { handleSubmit, handleReset } = useForm({
  validationSchema,
});
const { value: firstName, errorMessage: firstNameErrorMessage } =
  useField("first_name");
const { value: lastName, errorMessage: lastNameErrorMessage } =
  useField("last_name");
const { value: phone, errorMessage: phoneErrorMessage } = useField("phone");
const { value: email, errorMessage: emailErrorMessage } = useField("email");
const { value: company, errorMessage: companyErrorMessage } =
  useField("company");
const { value: website, errorMessage: websiteErrorMessage } =
  useField("website");

const submit = handleSubmit(async (values) => {
  loading.value = true;
  const newObj = JSON.stringify(values);
  const { isFinished } = await useFetch(`${API_URL}`, {
    method: "POST",
    headers: {
      Accept: "application.json",
      "Content-Type": "application/json",
    },
    body: newObj,
  });
  if (isFinished.value) {
    loading.value = false;
    showSnackbar.value = true;
    handleReset();
  }
});
</script>

<style scoped lang="scss">
.pointer {
  cursor: pointer;
}
</style>
```
