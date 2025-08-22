<script lang="ts" setup>
import { computed } from 'vue'

interface CalendarEmits {
  (e: 'day:select', val: { date: Date; day: number; index: number; format: string }): void
}

const { locale = 'ru' } = defineProps<{ locale?: Intl.LocalesArgument }>()
const model = defineModel<string>()
const emits = defineEmits<CalendarEmits>()

const getDate = computed(() => {
  if (!model.value) {
    return new Date()
  }

  if (isNaN(Date.parse(model.value))) {
    throw new Error('Date invalid')
  }

  return new Date(model.value)
})

const getDays = computed(() => {
  return new Date(getDate.value.getFullYear(), getDate.value.getMonth() + 1, 0).getDate()
})

const getFirstDay = computed(() => {
  const date = new Date(getDate.value)
  date.setDate(1)

  return date
})

const getLocale = computed(() => {
  try {
    Intl.getCanonicalLocales(locale.toString())
    return locale
  } catch (err) {
    return 'ru'
  }
})

const getLocaleYear = computed(() => {
  return new Intl.DateTimeFormat(getLocale.value, { year: 'numeric', month: 'long' }).format(getDate.value)
})

const getLocaleWeeks = computed(() => {
  const date = new Date('2023-01-01')
  const intl = new Intl.DateTimeFormat(getLocale.value, { weekday: 'short' })

  return Array.from({ length: 7 }, () => {
    date.setDate(date.getDate() + 1)
    return intl.format(date)
  })
})

function onDaySelect(day: number, index: number) {
  const date = new Date(getDate.value)

  date.setDate(day)
  model.value = formatDate(date)
  emits('day:select', { date, day, index, format: formatDate(date) })
}

function onSwitchMonth(next: boolean) {
  const offset = next ? 1 : -1
  const date = new Date(getDate.value)

  if (date.getMonth() === (next ? 11 : 0)) {
    date.setFullYear(getDate.value.getFullYear() + offset)
    date.setMonth(next ? 0 : 11)
  } else {
    date.setMonth(getDate.value.getMonth() + offset)
  }

  model.value = formatDate(date)
}

function formatDate(date: Date) {
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  const day = String(date.getDate()).padStart(2, '0')

  return `${year}-${month}-${day}`
}
</script>

<template>
  <table :class="S.calendar">
    <thead :class="S.head">
      <tr :class="S.switchMonth">
        <td @click="onSwitchMonth(false)">&#9668;</td>
        <td :class="S.year">{{ getLocaleYear }}</td>
        <td @click="onSwitchMonth(true)">&#9658;</td>
      </tr>

      <tr :class="S.tr">
        <td v-for="week in getLocaleWeeks" :key="week" :class="S.td">{{ week }}</td>
      </tr>
    </thead>

    <tbody>
      <tr :class="S.tr">
        <td v-for="empty in Math.max(0, getFirstDay.getDay() - 1)" :key="empty" :class="S.td" />
        <td
          v-for="(day, index) in getDays"
          @click="onDaySelect(day, index)"
          :class="[S.td, S.day, day == getDate.getDate() && S.active]"
        >
          {{ day }}
        </td>
      </tr>
    </tbody>
  </table>
</template>

<style module="S">
.calendar {
  background: #fff;
  min-width: 320px;
  padding: 0.5rem;
  border-radius: 0.2rem;
  box-shadow: 0.01rem 0.2rem 0.4rem oklch(37.2% 0.044 257.287 / 0.05);
}

.head {
  display: grid;
  gap: 0.5rem;
}

.year::first-letter {
  text-transform: uppercase;
}

.switchMonth {
  display: grid;
  grid-template-columns: auto 1fr auto;
  justify-items: center;
  align-items: center;
  gap: 1rem;
}

.body {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  justify-content: center;
}

.day {
  cursor: pointer;
  transition: color 0.2s;
  border-radius: 0.1rem;

  &:hover {
    color: oklch(67.3% 0.182 276.935);
  }

  &.active {
    color: oklch(67.3% 0.182 276.935);
    outline: 1px solid oklch(67.3% 0.182 276.935);
  }
}

.tr {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  justify-content: center;
  text-align: center;
}

.td {
  display: grid;
  place-content: center;
  aspect-ratio: 1/1;
}
</style>
