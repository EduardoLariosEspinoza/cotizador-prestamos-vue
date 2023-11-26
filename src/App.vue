<script setup>
import { ref, computed, watch } from "vue";
import Header from "./components/Header.vue";
import Button from "./components/Button.vue";
import { calcularTotalPagar } from "./helpers";

// Si quitamos setup, se utiliza esta sintaxis para importar
/* export default {
  components: {
    Header
  }
} */

const cantidad = ref(10000);
const meses = ref(6);
const total = ref(calcularTotalPagar(0));
const MIN = 0;
const MAX = 20000;
const STEP = 100;

// Importar antes reactive
/* const state = reactive({
  cantidad: 0,
}) */

// computed se manda a llamar cuando se actualiza el valor de una variable reactiva que se utiliza dentro de computed
// const formatearDinero = computed(() => {

const formatearDinero = (valor) => {
  const formatter = Intl.NumberFormat("en-US", {
    style: "currency",
    currency: "USD",
  });

  return formatter.format(valor);
};

watch([cantidad, meses], () => {
  total.value = calcularTotalPagar(cantidad.value, meses.value); // En <script> debemos usar variable.value, en el <template> solo variable
}, {
  immediate: true
}) // immediate: true hace que el watch se ejecute al inicio de la app

const pagoMensual = computed(() => {
  return total.value / meses.value;
});

const handleChangeDecremento = () => {
  const valor = cantidad.value - STEP;

  if (valor < MIN) {
    alert(`El valor no puede ser menor a ${MIN}`);

    return;
  }

  cantidad.value = valor;
}

const handleChangeIncremento = () => {
  const valor = cantidad.value + STEP;

  if (valor > MAX) {
    alert(`El valor no puede ser mayor a ${MAX}`);

    return;
  }

  cantidad.value = valor;
}

</script>

<template>
  <div class="my-20 max-w-lg mx-auto bg-white shadow p-10">
    <Header />

    <div class="flex justify-between mt-10">

      <Button :operador="'-'" @fn="handleChangeDecremento" />

      <Button :operador="'+'" @fn="handleChangeIncremento" />
    </div>

    <div class="my-5">
      <input type="range" :min="MIN" :max="MAX" :step="STEP"
        class="w-full bg-gray-200 accent-lime-500 hover:accent-lime-600" v-model.number="cantidad" />

      <p class="text-center my-10 text-5xl font-extrabold text-indigo-600">
        {{ formatearDinero(cantidad) }}
      </p>
      <!-- Funciona igual que la anterior <p v-text=`$ ${cantidad}`></p> -->

      <h2 class="text-2xl font-extrabold text-gray-500 text-center">
        Selecciona el <span class="text-indigo-600">Plazo</span> a pagar
      </h2>

      <select
        class="w-full p-2 bg-white border border-gray-300 rounded-lg text-center text-xl font-bold text-gray-500 mt-5"
        :value="meses" v-model.number="meses">
        <!-- v-model entrelaza un valor a un input, cada que el input cambia se actualiza el valor y viceversa -->
        <option value="6">6 Meses</option>
        <option value="12">12 Meses</option>
        <option value="24">24 Meses</option>
      </select>

    </div>

    <!-- space-y-3 le da separacion en 'y' a los hijos -->
    <!-- <div v-if="total > 0" class="my-5 space-y-3 bg-gray-50 p-5"> -->
    <div class="my-5 space-y-3 bg-gray-50 p-5">
      <h2 class="text-2xl font-extrabold text-gray-500 text-center">Resumen de <span class="text-indigo-600">pagos</span>
      </h2>

      <p class="text-xl text-gray-500 text-center font-bold">{{ meses }} Meses</p>
      <p class="text-xl text-gray-500 text-center font-bold">Total: {{ formatearDinero(total) }} </p>
      <p class="text-xl text-gray-500 text-center font-bold">Mensuales: {{ formatearDinero(pagoMensual) }}</p>
    </div>

    <!-- <p v-else class="text-center">Añade una cantidad y un plazo a pagar</p> -->
  </div>
</template>

<!-- v-model.number="cantidad" reemplaza :value="cantidad" y @input="handleChange" (Que actualizaba el valor de cantidad cuando moviamos el input range) -->

<!-- scoped significa que solo aplica para este archivo -->
<!-- <style scoped> -->
