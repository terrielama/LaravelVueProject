<script setup lang="ts">
import { reactive, ref } from "vue";
import axiosInstance from "@/components/lib/axios";

interface RegisterForm {
  name: string;
  email: string;
  password: string;
  password_confirmation: string;
}

const form = reactive<RegisterForm>({
  name: "",
  email: "",
  password: "",
  password_confirmation: "",
});

// Stockage des erreurs par champ
const errors = reactive<Record<string, string[]>>({});

const register = async () => {
  try {
    // Reset des erreurs
    Object.keys(errors).forEach(key => delete errors[key]);

    // 1. Récupérer le cookie CSRF
    await axiosInstance.get("/sanctum/csrf-cookie", { withCredentials: true });

    // 2. Envoyer la requête POST
    const response = await axiosInstance.post("/api/register", form, { withCredentials: true });

    console.log("Inscription réussie :", response.data);

    // Réinitialiser le formulaire si besoin
    form.name = "";
    form.email = "";
    form.password = "";
    form.password_confirmation = "";
  } catch (error: any) {
    if (error.response?.status === 422) {
      // Copier les erreurs dans notre reactive
      Object.assign(errors, error.response.data.errors);
    } else {
      console.error("Erreur :", error.response?.data || error.message);
    }
  }
};
</script>

<template>
  <h1 class="text-2xl font-bold mb-6">Inscription</h1>
  
  <form @submit.prevent="register" class="max-w-md mx-auto">
    <div class="mb-5">
      <label for="name" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Your name</label>
      <input type="text" id="name" v-model="form.name"
             class="shadow-xs bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:text-white"
             placeholder="Your name" required />
    </div>

    <div class="mb-5">
      <label for="email" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Your email</label>
      <input type="email" id="email" v-model="form.email"
             class="shadow-xs bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:text-white"
             placeholder="name@flowbite.com" required />
    </div>

    <div class="mb-5">
      <label for="password" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Your password</label>
      <input type="password" id="password" v-model="form.password"
             class="shadow-xs bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:text-white"
             required />
    </div>

    <div class="mb-5">
      <label for="repeat-password" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Repeat password</label>
      <input type="password" id="repeat-password" v-model="form.password_confirmation"
             class="shadow-xs bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:text-white"
             required />
    </div>

    <div class="flex items-start mb-5">
      <input id="terms" type="checkbox"
             class="w-4 h-4 border border-gray-300 rounded-sm bg-gray-50 focus:ring-3 focus:ring-blue-300 dark:bg-gray-700 dark:border-gray-600 dark:focus:ring-blue-600 dark:ring-offset-gray-800 dark:focus:ring-offset-gray-800"
             required />
      <label for="terms" class="ms-2 text-sm font-medium text-gray-900 dark:text-gray-300">
        I agree with the <a href="#" class="text-blue-600 hover:underline dark:text-blue-500">terms and conditions</a>
      </label>
    </div>

    <button type="submit"
            class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">
      Register new account
    </button>
  </form>
</template>
