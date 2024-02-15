<script setup>
import { ref, computed, watch } from 'vue';


const props = defineProps(['cardProducts']);

const total = computed(()=> {
  return props.cardProducts.reduce((total, p) => total+= p.price * p.amount, 0);
})

const emit = defineEmits([
  'removeFromCard', 
  'changeAmount', 
  'decrementAmount', 
  'incrementAmount'
]);

function onChangeAmount(product) {
  if (product.amount<=0) {
    emit('removeFromCard', product.id);
  } else {
    emit('changeAmount', product.id, product.amount);
  }
}
</script>

<template>
  <div>
    <h2>Carrito de compra</h2>
    <table>
      <thead>
        <tr>
          <th></th>
          <th>id</th>
          <th>Nombre</th>
          <th>Precio</th>
          <th>Cantidad</th>
          <th>Subtotal</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in cardProducts" :key="product.id">
          <td>
            <button @click="$emit('removeFromCard', product.id)">X</button>
          </td>
          <td>{{ product.id }}</td>
          <td>{{ product.name }}</td>
          <td>{{ product.price }}</td>
          <td>
            <input type="number" v-model="product.amount" @change.lazy="onChangeAmount(product)">
            <button @click="$emit('decrementAmount', product.id)">-</button>
            <button @click="$emit('incrementAmount', product.id)">+</button>
          </td>
          <td>{{ (product.price * product.amount).toFixed(2) }}</td>
        </tr>
      </tbody>
      <tfoot>
        <tr>
          <th colspan="5">TOTAL</th>
          <td>{{ total.toFixed(2) }}</td>
        </tr>
      </tfoot>
    </table>
  </div>
</template>

<style scoped>
div {
  background-color: azure;
  padding: 5px;
}

input {
  width: 40px;
  text-align: right;
}
</style>