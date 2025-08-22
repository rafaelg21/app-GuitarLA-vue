<script setup>
import { ref,  onMounted, watch } from 'vue';
import {db} from './data/guitarras.js';
import Guitarra from './components/Guitarra.vue';
import Header from './components/Header.vue';
import Footer from './components/Footer.vue';



const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});
const guitarraSlide = ref(0);

watch(carrito, () => {
    guardarLocalStorage();
}, 
{ 
    deep: true 
});
const randomInt = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1)) + min;
};
onMounted(() => {   
    guitarras.value = db;
    guitarraSlide.value = randomInt(0, guitarras.value.length - 1);
    guitarra.value = db[guitarraSlide.value]; // Asignar la primera guitarra como ejemplo
  
    const carritoStorage = localStorage.getItem('carrito');
    if(carritoStorage) {
        carrito.value = JSON.parse(carritoStorage);
    }
});

const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value));
}

const agregarCarrito = (guitarra) => {
    const existeCarrito =carrito.value.findIndex(producto => producto.id === guitarra.id);
    if(existeCarrito !== -1) {
        carrito.value[existeCarrito].cantidad++;     
        return;   
    }
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
        
};

const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id);
    if(carrito.value[index].cantidad <= 1) return;
    carrito.value[index].cantidad--;
};

const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id);
    carrito.value[index].cantidad++;
};

const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id);
    //opcion 2:
    //const index = carrito.value.findIndex(producto => producto.id === id);
    //carrito.value.splice(index, 1);
};

const vaciarCarrito = () => {
    carrito.value = [];
};

</script>

<template>

    <Header
     :carrito="carrito"
     :guitarra="guitarra"
     @incrementar-cantidad="incrementarCantidad"
     @decrementar-cantidad="decrementarCantidad"
     @agregar-carrito="agregarCarrito(guitarra)"
     @eliminar-producto="eliminarProducto"
     @vaciar-carrito="vaciarCarrito"
    />

        <main class="container-xl mt-5">
            <h2 class="text-center">Nuestra Colección</h2>

            <div class="row mt-5">
                <Guitarra 
                v-for="guitarra in guitarras"
                :key="guitarra.id"
                :guitarra="guitarra"
                @agregar-carrito="agregarCarrito(guitarra)"
                />
            </div>
        </main>

    <Footer />   

</template>

<style scoped>

</style>
