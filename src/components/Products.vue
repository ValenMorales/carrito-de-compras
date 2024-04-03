<template>
  <header>
    <h1>Restaurant</h1>
    <img src="../../pizza.jpg" alt="" @click="showCar = !showCar">
  </header>
  <section>
  <div class="products">
    <div class="item" v-for="product in products" :key="product.id">
      <span class="title">{{ product.name }}</span>
      <img class="image" :src="'../../' + product.imageUrl" alt="product image"  />
      <span class="price">{{ product.price }}</span>
      <button @click="addProduct(product)" class="button-item">Agregar al Carrito</button>
    </div>
  </div>
    <div v-if="showCar" class="carrito" id="carrito">
            <div class="header-carrito">
                <h2>Tu Carrito</h2>
            </div>
            <div class="carrito-items">
                <div class="carrito-item" v-for="product in carproducts" :key="product.id">
                  <img  class="carrito-item-image" :src="'../../public/' + product.imageUrl" alt="product image"  >
                    <div class="carrito-item-detalles">
                        <span class="carrito-item-titulo">{{ product.name }}</span>
                        <div class="selector-cantidad">
                            <p @click="restCantity(product, product.id)" class="cantity">-</p>
                          <p class="carrito-item-cantidad">{{ product.cantity }}</p>
                           <p @click="sumCantity(product, product.id)" class="cantity">+</p>
                        </div>
                        <span class="carrito-item-precio">{{ product.price }}</span>
                    </div>
                   <span class="eliminar">
                        <img src="../../trash.png" alt="" @click="deleteItem(product)">
                   </span>
                </div>
                 
            </div>
            <div class="carrito-total">
                <div class="fila">
                    <strong>Tu Total</strong>
                    <span class="carrito-precio-total">
                        {{ total }}
                    </span>
                </div>
                <button class="btn-pagar">Pagar <i class="fa-solid fa-bag-shopping"></i> </button>
            </div>
        </div>
      </section>

     
</template>

<script setup>
import { ref, onMounted } from 'vue';

const showCar = ref(false);
const total = ref(0);
const products = ref([]);
const carproducts = ref([]);

const deleteItem = (product) =>{
  const index = carproducts.value.indexOf(product);
  carproducts.value.splice(index, 1);
  total.value = total.value - (product.price * product.cantity);
}

const sumCantity = (product, id) =>{
  product.cantity = product.cantity + 1;
  total.value = total.value + products.value[id].price;
}

const restCantity = (product, id) =>{
  product.cantity = product.cantity - 1;
  total.value = total.value - products.value[id].price;
}

const addProduct = (product) =>{
  carproducts.value = [...carproducts.value, product];
  total.value = total.value + product.price;
}

onMounted(async () => {
  try {
    const response = await fetch("http://localhost:8000/products");
    const data = await response.json();
    products.value = data.map(product => ({
      id: product.id,
      name: product.name,
      price: product.price,
      imageUrl : product.imageUrl,
      cantity: 1
    }));
  } catch (error) {
    console.error("Error fetching products:", error);
  }
});
</script>

<style scoped>

section {
  display: flex;
  width: 90vw;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100px;
  width: 90vw;
}

header img {
  width: 50px;
  height: 50px;
  cursor:pointer;
}

 .products{
   
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(15rem, 1fr));
    grid-gap:1.5rem;
    grid-row-gap: 1.5rem;
    width: 90vw;
    transition: .3s;
}
 .item{
    max-width: 250px;
    margin: auto;
    border: 1px solid #666;
    border-radius: 10px;
    padding: 20px;
    transition: .3s;
}
.image{
    width: 100%;
}
 .item:hover{
    box-shadow: 0 0 10px #666;
    scale: 1.05;
}
.title{
    display: block;
    font-weight: bold;
    text-align: center;
    text-transform: uppercase;
}
.price{
    display: block;
    text-align: center;
    font-weight: bold;
    font-size: 22px;
}

.button-item{
    display: block;
    margin: 10px auto;
    border: none;
    background-color: black;
    color: #fff;
    padding: 5px 15px;
    border-radius: 5px;
    cursor: pointer;
}



/* seccion carrito */
.carrito{
    border: 1px solid #666;
    width: 35%;
    border-radius: 10px;
    transition: .3s;

}
.carrito .header-carrito{
    background-color: #000;
    color: #fff;
    text-align: center;
    padding: 30px 0;
}
 .carrito-item{
    display: flex;
    align-items: center;
     justify-content: space-around; 
    position: relative;
    border-bottom: 1px solid #666;
    padding: 20px;
}
 .carrito-item-image{
    width: 80px;
    height: 80px;
}
 .carrito-item-detalles{
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

.cantity{
  width: 30px;
  height: 30px;
  border: 1px solid #666;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  cursor: pointer;
  font-size: 20px;
  font-style: bold;
  font-weight: bold;
}

 .carrito-item-titulo{
    display: block;
    font-weight: bold;
    margin-bottom: 10px;
    text-transform: uppercase;
}
 .selector-cantidad{
    display: flex;
    align-items: center;
    justify-content: center;
}
.carrito .carrito-item .carrito-item-cantidad{
    border: none;
    font-size: 18px;
    background-color: transparent;
    display: inline-block;
    width:30px;
    padding: 5px;
    text-align: center;
}
.carrito .carrito-item .carrito-item-precio{
    font-weight: bold;
    display: inline-block;
    font-size: 18px;
    margin-bottom: 5px;
}
.carrito .carrito-item .btn-eliminar{
    position: absolute;
    right: 15px;
    top: 15px;
    color: #000;
    font-size: 20px;
    width: 40px;
    height: 40px;
    line-height: 40px;
    text-align: center;
    border-radius: 50%;
    border: 1px solid #000;
    cursor: pointer;
    display: block;
    background: transparent;
    z-index: 20;
}
.carrito .carrito-item .btn-eliminar i{
    pointer-events: none;
}

.carrito-total{
    background-color: #f3f3f3;
    padding: 30px;
}
.carrito-total .fila{
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 22px;
    font-weight: bold;
    margin-bottom: 10px;
}
.carrito-total .btn-pagar{
    display: block;
    width: 100%;
    border: none;
    background: #000;
    color: #fff;
    border-radius: 5px;
    font-size: 18px;
    padding: 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    cursor: pointer;
    transition: .3s;
}
.carrito-total .btn-pagar:hover{
    scale: 1.05;
}
</style>
