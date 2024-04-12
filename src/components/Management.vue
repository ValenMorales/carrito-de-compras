<script setup>
import { ref } from "vue";
import Forms from "./Forms.vue";
import LoadingSpinner from "./LoadingSpinner.vue";
const props = defineProps(["products", 'loading']);
const emit = defineEmits(["cargarProductos"]);
const showNotFound = ref(false);
const mostrarFormularioEdicion = ref(false);
const mostrarFormularioCreacion = ref(false);
const specificProduct = ref({
  id: null,
  name: "",
  description: "",
  price: 0,
  stock: 0,
  imageUrl: "",
});

const actualizar = () => {
  emit("cargarProductos");
};

const eliminarProducto = async (productId) => {
  try {
    const response = await fetch(
      `http://localhost:8000/products/${productId}`,
      {
        method: "DELETE",
      }
    );

    if (response.ok) {
      emit("cargarProductos");
      mostrarFormularioEdicion.value= false;
      mostrarFormularioCreacion.value= false ; 
      
           

    } else {
      console.error("Error al eliminar el producto:", response.statusText);
    }
  } catch (error) {
    console.error("Error al eliminar el producto:", error.message);
  }
};


const product = ref({
  id: null,
  name: "",
  description: "",
  price: 0,
  stock: 0,
});

const formularioEdicion = (productId) => {
  product.value = props.products.find((product) => product.id == productId);
  mostrarFormularioEdicion.value = true;
  mostrarFormularioCreacion.value = false;
};

const formularioCreacion = () => {
  mostrarFormularioEdicion.value = false;
  mostrarFormularioCreacion.value = true;
};

const hayImagen = (product) => {
  return product.imageUrl != "" ? true : false;
};

const buscarProducto = (busqueda) =>{
  const byId = props.products.find((product) => product.id == busqueda);
  const byName = props.products.find((product) => product.name.toLowerCase() === busqueda.toLowerCase());

  const productId = ref(0)
  if(byId){
    productId.value = byId.id;
    
  }else if(byName){
    productId.value = byName.id;
  } else {
    showNotFound.value = true 
    specificProduct.value ={
      id: null,
      name: "",
      description: "",
      price: 0,
      stock: 0,
      imageUrl: "",
    }
    return;
  }
  showNotFound.value = false;
  buscarEspecifico(productId.value);
}

const buscarEspecifico = async (productId) => {
  try {
    const response = await fetch(
      `http://localhost:8000/products/${productId}`
    );

    if (response.ok) {
      const data = await response.json();
      specificProduct.value = data 
    } else {
      console.error("Error al buscar el producto:", response.statusText);
    }
  } catch (error) {
    console.error("Error al buscar el producto:", error.message);
  }
};

const hayProduct = () => {
  return specificProduct.value.id != null ? true : false;
}

</script>

<template>
  <div class="principal">
    <h2>Gestiona los productos de tu tienda</h2>
  </div>

  <button @click="formularioCreacion()" class="crear">Crear producto</button>

  <div class="management">
    <div class="menu">
      <h4>Buscar un producto por su id o nombre</h4>
      <div class="search">
        
        <div class="search-bar">
          <input type="text" v-model="search" />
          <button @click="buscarProducto(search)">Buscar</button>
        </div>
      </div>
      <div v-if="showNotFound" class="not-found">
        <p>No se encontraron resultados para tu busqueda</p>
      </div>
        <div v-if="hayProduct()" class="item">
              <span class="span">{{ specificProduct.name }}</span>
          <img
            class="image"
            :src="'../../' + specificProduct.imageUrl"
            alt="product image"
          />
          <span class="span">
           $ {{ specificProduct.price }}
          </span>
          <span class="span">
            {{ specificProduct.expiration }}
          </span>

        
        </div>
     
    </div>
    <table class="table table-dark table-hover">
      <thead>
        <tr>
          <th scope="col">ID</th>
          <th scope="col">Producto</th>
          <th scope="col">Imagen</th>
          <th scope="col">Precio</th>
          <th scope="col">Expiracion</th>
          <th scope="col">Acción</th>
        </tr>
      </thead>
      <tbody>
        <LoadingSpinner v-if="loading" />
        <tr v-else v-for="product in products">
          <td>{{ product.id }}</td>
          <td>{{ product.name }}</td>

          <td>
            <img
              v-if="hayImagen(product)"
              class="image"
              :src="'../../' + product.imageUrl"
              alt="product image"
            />
            <p class="p" v-else>no tiene imagen</p>
          </td>

          <td>{{ product.price }}</td>
          <td>{{ product.expiration }}</td>
          <td>
            <div class="buttons">
              <button @click="formularioEdicion(product.id)">editar</button>
              <button @click="eliminarProducto(product.id)">eliminar</button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <Forms
    :mostrarFormularioEdicion="mostrarFormularioEdicion"
    :mostrarFormularioCreacion="mostrarFormularioCreacion"
    :product="product"
    :products="products"
    @producto-actualizado="actualizar()"
  />
</template>

<style scoped>
.search {
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.search-bar {
  display: flex;
}

.search-bar input{
 
  padding: 10px;
  border-radius: 5px;
  outline: none;
}

.search-bar button {

  padding: 10px;
  border-radius: 5px;
  text-align: center;
  justify-content: center;
  font-size: large;
  background-color: rgb(255, 230, 0);
  color: black;
}

.principal {
  display: flex;
  justify-content: center;
}

.management {
  display: flex;
}

.management .menu {
  width: 35%;
}

.menu {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
}

th {
  text-align: center;
}

td img {
  width: 50px;
  height: 50px;

}

td p{
  width: 80px;
  text-align: center;

}

td {
  text-align: center; 
}

.table {
  margin: 2rem;
}

.table-product {
  display: flex;
  justify-content: center;
  align-items: center;
}


.buttons {
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

.buttons button:nth-child(1) {
  background-color: #007bff;
  color: white;
}

.buttons button:nth-child(2) {
  background-color: #dc3545;
  color: white;
}

h2 {
  text-align: center;
  margin-bottom: 20px;
  margin-top: 20px;
  font-size: 30px;
  font-weight: 600;
  color: #000000;
}

.item {
  max-width: 250px;
  margin: auto;
  margin-top: 20px;
  border: 1px solid #666;
  border-radius: 10px;
  padding: 20px;
  transition: 0.3s;
}
.image {
  width: 100%;
}
.item:hover {
  box-shadow: 0 0 10px #666;
  scale: 1.05;
}
.span {
  display: block;
  font-weight: bold;
  text-align: center;
  text-transform: uppercase;
}


.button-item {
  display: block;
  margin: 10px auto;
  border: none;
  background-color: black;
  color: #fff;
  padding: 5px 15px;
  border-radius: 5px;
  cursor: pointer;
}
button{
  transition: 1s;
}

.menu h4{
  text-align: center;
  margin-bottom: 20px;
  margin-top: 20px;
  font-size: 30px;
  font-weight: 600;
  color: #000000;
}

.crear {
  display: block;
  margin-left: auto;
  margin-right: 2rem;
  border: none;
  background-color: green;
  color: #fff;
  padding: 5px 15px;
  border-radius: 5px;
  cursor: pointer;
  transition: 1s;
}

button:hover{
 transform: scale(1.1);
}

.not-found {
  width: 15rem;
  font-size: larger;
  text-align: center;
  margin-top: 1rem;
}

</style>
