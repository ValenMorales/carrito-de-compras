<script setup>
import { ref } from "vue";
const props = defineProps([ 'products'])

const eliminarProducto = async (productId) => {
  try {
    const response = await fetch(`http://localhost:8000/products/${productId}`, {
      method: 'DELETE'
    });

    if (response.ok) {
      props.products.value = props.products.value.filter(product => product.id !== productId);
    } else {
      console.error('Error al eliminar el producto:', response.statusText);
    }
  } catch (error) {
    console.error('Error al eliminar el producto:', error.message);
  }
};

const enviarEdicion = async (product) =>{
  try {
    const response = await fetch(`http://localhost:8000/products/${product.id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(product)
    });

    if (response.ok) {
      console.log("funciono")
      
    } else {
      console.error('Error al actualizar el producto:', response.statusText);
    }
  } catch (error) {
    console.error('Error al actualizar el producto:', error.message);
  }
  
}

const enviarCreacion = async (productName, productPrice) => {
  try {
    const productCreate = {
      name: productName,
        price: productPrice,
        expiration: "cualquiera",
        imageUrl: "cualquiera"
    }
    console.log(productCreate)
    const response = await fetch(`http://localhost:8000/products`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(productCreate)
    });

    if (response.ok) {
      console.log("Funcionó");
    } else {
      console.error('Error al crear el producto:', response.statusText);
    }
  } catch (error) {
    console.error('Error al crear el producto:', error.message);
  }
};



const mostrarFormularioEdicion = ref(false);
const mostrarFormularioCreacion = ref(false);
const product = ref({
  id: null,
  name: '',
  description: '',
  price: 0,
  stock: 0
})

const formularioEdicion = (productId) => {
   product.value = props.products.find(product => product.id == productId);
  mostrarFormularioEdicion.value = true;
  mostrarFormularioCreacion.value = false;
};


const formularioCreacion = () => {
  mostrarFormularioEdicion.value = false;
  mostrarFormularioCreacion.value = true;
};


</script>

<template>
  <div class="principal">
    <h2>Aquí puedes gestionar tus productos</h2>
  </div>

  <div class="search">
  <div class="search-bar">
    <input type="text" v-model="search">
    <button @click="searchProducts">Buscar</button>
  </div>
  <button @click="formularioCreacion()" class="crear">Crear producto</button>
</div>



  <div class="management">
    <div  class="menu">
      <p class="estadisticas">estadisticas</p>
      <p class="eliminar">eliminar</p>

      
    </div>
    <table class="table table-dark table-hover">
    <thead>
    <tr>
      <th scope="col">Producto</th>
      <th scope="col">Imagen</th>
      <th scope="col">Precio</th>
      <th scope="col">Acción</th>
      
    </tr>
  </thead>
  <tbody>
    <tr v-for="product in products">
      <td>{{ product.name }}</td>
      <td> <img class="image" :src="'../../' + product.imageUrl" alt="product image"  /></td>
      <td>{{ product.price }}</td>
      <td > <div class="buttons">
        <button @click="formularioEdicion(product.id)">editar</button> <button @click="eliminarProducto(product.id)" >eliminar</button>
      </div>
      </td>

    </tr>
  </tbody>
</table>

</div>

<div v-if="mostrarFormularioEdicion" class="formulario">
  <form @submit.prevent="enviarEdicion(product)">
        <input type="text" placeholder="Nombre del producto" v-model="product.name">
        <input type="number" placeholder="Precio del producto" v-model="product.price">
        <input type="text" placeholder="Descripción del producto" v-model="product.description">
        <button type="submit">Editar</button>
      </form>
</div>

<div v-if="mostrarFormularioCreacion" class="formulario">
  <form @submit.prevent="enviarCreacion(nombreProducto, precioProducto)">
      <input type="text" placeholder="Nombre del producto" v-model="nombreProducto" >
      <input type="text" placeholder="Precio del producto" v-model="precioProducto">
      <input type="text" placeholder="Descripción del producto">
      <button type="submit">Crear</button>
    </form>
</div>


  
</template>

<style scoped>

.search {
  display: flex;
  justify-content: space-around;
  align-items: center;

  margin-left: 22rem ;
}

.search-bar {

  display: flex;
}


.principal{
  display:flex;
  justify-content: center;

}

.management{
  display: flex;
}


.management .menu{
  width: 35%;
}

th{
  text-align: center;
}

td img {
  width: 50px;
  height: 50px;
}

td{
  text-align: center;
}

.table {
  margin: 2rem;
}

.table-product{
  display: flex;
  justify-content: center;
  align-items: center;
}

.table-product p{
  margin-right: 20px;
}

.buttons{
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.buttons button {
  margin: 0 10px;
  padding: 5px 10px;
  border-radius: 5px;
  border: 1px solid #ccc;

  cursor: pointer;
  font-size: 15px;
  font-weight: bold;
}

.buttons button:nth-child(1){
  background-color: #007bff;
  color: white;
}

.buttons button:nth-child(2){
  background-color: #dc3545;
  color: white;
}


h2{
  text-align: center;
  margin-bottom: 20px;
  margin-top: 20px;
  font-size: 30px;
  font-weight: 600;
  color: #000000;
}
</style>