<script setup>
import {ref} from "vue"

const props = defineProps(['products', 'product', 'mostrarFormularioEdicion', 'mostrarFormularioCreacion'])
const emit = defineEmits(['producto-actualizado']);
const seleccionarImagen = (imageUrl) => {
        props.product.imageUrl = imageUrl; 
    }
const enviarEdicion = async (product) =>{

  console.log(product)
   
  try {
    const response = await fetch(`http://localhost:8000/products/${product.id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(product)
    });

    if (response.ok) {

      emit('producto-actualizado');
      
    } else {
      console.error('Error al actualizar el producto:', response.statusText);
    }
  } catch (error) {
    console.error('Error al actualizar el producto:', error.message);
  }
  
}

const enviarCreacion = async (productName, productPrice, expiracion, imageProduct) => {
  try {
    const productCreate = {
      name: productName,
        price: productPrice,
        expiration: expiracion,
        imageUrl: imageProduct
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
       emit('producto-actualizado');
    
    } else {
      console.error('Error al crear el producto:', response.statusText);
    }
  } catch (error) {
    console.error('Error al crear el producto:', error.message);
  }
};


</script>

<template>
     <div v-if="mostrarFormularioEdicion" class="modal-overlay">
    <form @submit.prevent="enviarEdicion(product)"
      >
        <div class="header-form">
            <h2>Edita tu producto</h2>
        <img @click="mostrarFormularioEdicion = false" src="/boton-x.png" alt="">
        </div>
        
        <div class="input-container">
            <label for="name">Nombre</label>
            <input type="text" id="name" v-model="product.name">
        </div>
        <div class="input-container">
            <label for="price">Precio</label>
            <input type="number" id="price" v-model="product.price" min="0">
        </div>
        <div class="input-container">
            <label for="expiration">Vencimiento</label>
            
            <input type="date" id="expiration" v-model="product.expiration" min="2024-04-10" >
        </div>
        <div class="productImage" >
            <label for="imageUrl">Imagen</label>
            <img :src="product.imageUrl">
        </div>
          <div  class="imagenes">
            <p>Puedes elegir otra imagen de la galeria de productos</p>
            <img @click="seleccionarImagen(element.imageUrl)" v-for="element in products" :src="element.imageUrl" alt="imagen del producto">
          </div>
          <button  class="submit-button-1" type="submit"    >Editar</button>
        </form>
  </div>
  
  <div v-if="mostrarFormularioCreacion" class="modal-overlay">
    <form @submit.prevent="enviarCreacion(nombreProducto, precioProducto, expiracion, imageProduct)"> 
        <div class="header-form">
            <h2>Crea un producto</h2>
        <img @click="mostrarFormularioCreacion = false" src="/boton-x.png" alt="">
        </div>
        <div class="input-container">
            <label for="name">Nombre</label>
            <input required type="text" id="name" v-model="nombreProducto">
        </div>
        <div class="input-container">
            <label for="price">Precio</label>
            <input required type="number" id="price" v-model="precioProducto" min="0">
        </div>
        <div class="input-container">
            <label for="expiration">Vencimiento</label>
           
            <input type="date" id="expiration" v-model="expiracion" min="2024-04-10">
        </div>
          <div  class="imagenes">
            <p>Puedes elegir una imagen de la galeria de productos</p>
            <img @click="imageProduct = element.imageUrl" v-for="element in products" :src="element.imageUrl" alt="imagen del producto">
          </div>
        <button  class="submit-button-2" type="submit">Crear</button>
      </form>
  </div>

  
</template>

<style scoped>


.imagenes{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-evenly;
}

.imagenes img {
    cursor: pointer;
    border-radius: 10px;
  max-width: 100px;
  max-height: 100px;
}

form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin: 10px;
    padding: 10px;
    border-radius: 10px;
    width :30rem;
    background-color: white;
}

.input-container{
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-radius: 10px;
    background-color: white;
    width:20rem;
}
.productImage{
    display:flex;
    justify-content: space-evenly;
    align-items: center;
    width: 20rem;
}
.productImage img{
 
    border-radius: 10px;
  max-width: 100px;
  max-height: 100px;
}
.input-container label{
    width: 100px;
    font-style: bold;
}

form input{
    margin: 10px;
    padding: 10px;
    border-radius: 10px;
    width :20rem;
    background-color: white;
}


/*stylos modal*/
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2;
}

.submit-button-1, .submit-button-2{
    margin: 10px;
    padding: 10px;
    border-radius: 10px;
    width :20rem;
    background-color: white;
    font-size: large;
    font-weight: bold;
    transition: 2s;
}


.submit-button-1{
    background-color: #007bff;
}

.submit-button-2{
    background-color: green;
}


.header-form {
    display: flex;
}
.header-form img{
    width: 30px;
    height: 30px;
    position: fixed;
    right: 30rem;
    cursor:pointer;
}



</style>
