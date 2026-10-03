<script setup lang="ts">
import axios from 'axios';
import { onMounted, ref } from 'vue';

let clicks = ref<number>(0)
let otch = ref<boolean>(true)

let login = ref<string>("")
let password = ref<string>("")

let tema = (it: number) => {
  clicks.value += it
  otch.value = !otch.value
}

let getBooks = async () => {
    try {
        const res = await axios.get("https://stephen-king-api.onrender.com/api/books")
        console.log(res)
    } catch (err) {
        console.log(err)
    }
}

onMounted(() => {
    getBooks()
})

</script>

<template>
  <p>Добро пожаловать</p>

  <button @click="$router.push('/auth')">Авторизация</button>

  <input v-model="login" type="text">
  <input v-model="password" type="password">

  <p>Логин: {{ login }}</p>
  <p>Пароль: {{ password }}</p>

  <p v-if="otch">Отчисление</p>
  
  <p>Накликали: {{ clicks }}</p>
  <button @click="tema(i)" v-for="i in 100">Привет {{ i }}</button>
</template>

<style scoped></style>
