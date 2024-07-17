<script lang="ts" setup>
import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select';
import Label from '@/components/ui/label/Label.vue';
import Input from '@/components/ui/input/Input.vue';
import { computed, ref } from 'vue';

const summa = ref(0);
const modifier = ref('000000');
const exchangeRate = ref('0');

const convertedValue = computed(() => {
  const fullVal = Number(`${summa.value}${modifier.value}`);
  const converted = new Intl.NumberFormat('sv-SE', {
    style: 'currency',
    currency: 'SEK',
    maximumFractionDigits: 0,
  }).format(fullVal * parseFloat(exchangeRate.value));

  return converted;
});

const modifiers = [
  {
    value: '000000',
    label: 'Million',
  },
  {
    value: '000000000',
    label: 'Billion',
  },
];
</script>

<template>
  <div
    class="text-zinc-300 bg-zinc-950 w-full h-screen grid place-items-center"
  >
    <div class="w-full max-w-4xl bg-zinc-800 rounded-xl p-8">
      <h1 class="text-3xl mb-6">Konverta valuta till SEK</h1>

      <div class="flex flex-col gap-4">
        <div class="flex items-center">
          <Input id="summa" placeholder="Summa" v-model="summa" />
          <Select v-model="modifier">
            <SelectTrigger>
              <SelectValue placeholder="Välj modifier" />
            </SelectTrigger>
            <SelectContent>
              <SelectGroup>
                <SelectItem
                  v-for="({ value, label }, index) in modifiers"
                  :key="index"
                  :value="value"
                >
                  {{ label }}
                </SelectItem>
              </SelectGroup>
            </SelectContent>
          </Select>
        </div>
        <div class="flex items-center gap-2">
          <Label for="exchange">Växlingskurs</Label>
          <Input v-model="exchangeRate" id="exchange" class="text-center" />
          <Label for="exchange">SEK</Label>
        </div>
        <h2 class="text-5xl">{{ convertedValue }}</h2>
        <div class="flex flex-col text-xs">
          <span>Miljon: följs av 6 siffror</span>
          <span>Miljard: följs av 9 siffror</span>
          <span>Biljon: följs av 12 siffror</span>
        </div>
      </div>
    </div>
  </div>
</template>
