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
import { computed, ref, watch } from 'vue';
import { useQuery, useQueryClient } from '@tanstack/vue-query';

const summa = ref(0);
const currency = ref('EUR');
const modifier = ref('000000');

const queryClient = useQueryClient();

const { data } = useQuery({
  queryKey: ['exchange'],
  queryFn: async () => {
    const res = await fetch(
      `https://v6.exchangerate-api.com/v6/${
        import.meta.env.VITE_EXCHANGE_API_KEY
      }/latest/${currency.value}`
    );
    const json = await res.json();

    const rates = json?.conversion_rates ?? { SEK: '' };
    return rates.SEK;
  },
});

const convertedValue = computed(() => {
  const fullVal = Number(`${summa.value}${modifier.value}`);
  const converted = new Intl.NumberFormat('sv-SE', {
    style: 'currency',
    currency: 'SEK',
    maximumFractionDigits: 0,
  }).format(fullVal * parseFloat(data.value));

  return converted;
});

watch(currency, () => {
  queryClient.invalidateQueries({ queryKey: ['exchange'] });
});

const options = [
  {
    value: 'EUR',
    label: 'Euro',
  },
  {
    value: 'USD',
    label: 'US Dollar',
  },
];

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
          <Select v-model="currency">
            <SelectTrigger>
              <SelectValue placeholder="Välj valuta" />
            </SelectTrigger>
            <SelectContent>
              <SelectGroup>
                <SelectItem
                  v-for="({ value, label }, index) in options"
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
          <Input
            :disabled="true"
            v-model="data"
            id="exchange"
            class="text-center"
          />
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
