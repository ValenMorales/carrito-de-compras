<script setup>
import Products from "./components/Products.vue";
import Management from "./components/Management.vue";

import { ref, onMounted } from "vue";
const showManagement = ref(false);
const showProducts = ref(false);
const showHero=ref(true);
const loading = ref(true);

const products = ref([]);
const mostrarManagement = () =>{
  showManagement.value =!showManagement.value;
  showProducts.value = false;
  showHero.value = false;
}

const showHome = () =>{
  showManagement.value = false;
  showProducts.value = false;
  showHero.value = true;
}

const mostrarProducts = () =>{
  showProducts.value =!showProducts.value;
  showManagement.value = false;
  showHero.value = false;
}

const shouldLoad = ref(true);

const cargarProductos = async (shouldLoad) => {
  if (!shouldLoad) return;

  try {
    loading.value = true;
    const response = await fetch("http://localhost:8000/products");
    const data = await response.json();
    
    // Transformar los datos de los productos
    products.value = data.map((product) => ({
      id: product.id,
      name: product.name,
      price: product.price,
      imageUrl: product.imageUrl,
      expiration: product.expiration,
      cantity: 1,
    }));

    
  } catch (error) {
    console.error("Error solicitando los productos:", error);
  } finally {
      loading.value = false;
    }
  
};



onMounted(() => {
  cargarProductos(shouldLoad);
  shouldLoad.value = false;
});
</script>

<template>


  <header>

         
          
        <img @click="mostrarManagement()" src="/gerente.png">
        <img @click="mostrarProducts()" src="/compras.png">
        <img @click="showHome()" src="/ajustes.png">
   

      
      </header>

               
    <div class="hero" v-if="showHero">
      <h1>Tienda <span>V</span> </h1>
      <h2>Tu comida favorita</h2>
      <h2>Al instante</h2>
      
      <div class="buttons">
        <button @click="mostrarManagement()">Soy un admin</button>
        <button @click="mostrarProducts()">Soy un cliente</button>
      </div>
      
    </div>
    <div v-if="showProducts" class="products">
      <Products :products="products" :loading="loading"
       />
    </div>

    <Management :products="products"
    :loading="loading"
    @cargarProductos="cargarProductos(true)"
     v-if="showManagement"></Management>
    
  
</template>

<style scoped>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

header{
  background-color: rgb(12, 11, 11);
  width: 100%;
  height: 5rem;
  display: flex;
  justify-content: center;
  align-items: center;
  padding:10px;
}


header img {
  margin: 10px;
 
  padding: 10px 20px;
  cursor: pointer;
}


.hero {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100vw;
  height: 85vh;
  padding: 20px;
  background: url(/otro-fondo.jpg);
  background-size: cover;
  color:white;
}

.hero h1 {
  font-size: 4rem;
  font-weight: bold;
  
}

.hero h1 span {
 color: rgb(255, 230, 0);
 font-size: 6rem;
}

.hero h2{
  font-size: 2.5rem;
  font-weight: bold;
  
}

.buttons {
  display: flex;
}

.buttons button {
  margin: 10px;
  padding: 10px 20px;
  border: 1px solid black;
  border-radius: 5px;
  background-color: rgb(255, 230, 0);
  color: black;
  font-size: large;
  cursor: pointer;
}

.buttons button:hover {
  transform: scale(1.1);
  transition: 0.5s;
}



</style>
