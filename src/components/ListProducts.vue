<script setup>
import { ref, defineProps, computed, onMounted } from 'vue';

const searchWord = ref('');

const props = defineProps(['products']);

const filterdProducts = computed(() => {
  let products = props.products;
  if (categorySearch.value) {
    products = products.filter(p => p.category == categorySearch.value);
  }
  return products.filter(p => 
      p.name.toLocaleLowerCase().includes(
        searchWord.value.toLocaleLowerCase()))
      .sort((a,b) => {
        if (orderField.value == 'name') {
          return a.name.toLocaleLowerCase() < b.name.toLocaleLowerCase() ? -1 : 1;
        } 
        if (orderField.value == 'price') {
          return a.price - b.price;
        }
          return 0;
      });
});

const orderField = ref('name');

const categories = ref([])
const categorySearch = ref('');

onMounted(async () => {
  try {
    const response = await fetch('http://localhost:3000/api/categories');
    categories.value = (await response.json()).sort();
  } catch (error) {
    console.log('Error al cargar las categorías del servidor: ',error);
  }
});
</script>

<template>
  <div>
    <p>Filtrar por 
      <input type="text" v-model="searchWord">
      
      Ordenar por: 
      <input type="radio" id="name" value="name" v-model="orderField" />
      <label for="name">Nombre</label>

      <input type="radio" id="price" value="price" v-model="orderField" />
      <label for="price">Precio</label>
    </p>
    <p>Seleccionar categoria:
      <select v-model="categorySearch">
        <option value="">Cualquier categoría</option>
        <option v-for="category in categories">{{ category }}</option>
      </select>
    </p>
    <table v-if="filterdProducts.length">
      <thead>
        <tr>
          <th>id</th>
          <th>Nombre</th>
          <th>Categoría</th>
          <th>Precio</th>
          <th>Añadir a carrito</th>
          <th>Eliminar</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in filterdProducts" :key="product.id"
        :class="{warnig: product.category=='Alcoholic'}">
          <td>{{ product.id }}</td>
          <td>{{ product.category }}</td>
          <td>{{ product.name }}</td>
          <td>{{ product.price }}</td>
          <td>
            <button @click="$emit('addToCard', product.id)"
            >Añadir al carrito</button>
          </td>
          <td>
            <button @click="$emit('deleteProduct', product.id)"
              >Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>
    <h3 v-else>No existen productos para esta categoría.</h3>
  </div>
</template>

<style scoped>
  .warnig {
    background-color: lightpink;
  }
</style>