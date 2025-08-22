<script lang="ts" setup>
import { ref } from 'vue'
import Calendar from './Calendar.vue'

const date = ref() // yyyy-mm-dd
const locale = ref()

function alertDate(val: string) {
  alert(val)
}
</script>

<template>
  <div :class="S.layout">
    <form :class="S.form">
      <input v-model="locale" :class="S.input" placeholder="Любая валидная локаль (ru, en, de, ...)" />
    </form>

    <Calendar v-model="date" @day:select="alertDate(`Выбрано: ${$event.format}`)" :locale />
  </div>
</template>

<style>
body {
  margin: 0;
  color: oklch(27.9% 0.041 260.031);
}
</style>

<style module="S">
.layout {
  display: grid;
  place-content: center;
  gap: 1rem;
  height: 100vh;
  background: oklch(98.4% 0.003 247.858);
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  margin: 0;
}

.form {
  display: grid;
  align-items: center;
  grid-template-columns: 1fr auto;
  gap: 1rem;
  background: #fff;
  padding: 0 0.5rem;
  border-radius: 0.2rem;
  box-shadow: 0.01rem 0.2rem 0.4rem oklch(37.2% 0.044 257.287 / 0.05);
}

.input {
  width: 100%;
  height: 3rem;
  padding: 0;
  border: 0;
  color: inherit;
  font-family: inherit;
  background: none;
  outline: none;
}
</style>
