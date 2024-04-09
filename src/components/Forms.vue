<script>

</script>

<template>
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