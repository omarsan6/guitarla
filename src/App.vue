<script setup>

  import { ref, reactive, onMounted, watch } from 'vue';
  import { db } from './data/guitarras'
  import Guitarra from './components/Guitarra.vue';
  import Headers from './components/Headers.vue';
  import Footer from './components/Footer.vue';

  const guitarras = ref([])
  const carrito = ref([])
  const guitarra = ref({})

  watch(carrito, () => {
    guardarLocalStorage()
  },{
    deep: true
  })

  onMounted(() => {
    guitarras.value = db
    guitarra.value = db[3]

    const carritoStorage = localStorage.getItem('carrito')

    if(carritoStorage){
      carrito.value = JSON.parse(carritoStorage)
    }
  })

  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
  }

  const agregarCarrito = (guitarra) => {

    //busca si la guitarra existe en el arreglo carrito
    const existeCarrito = carrito.value.find(producto => producto.id === guitarra.id)

    if(!existeCarrito){
      //agregar por primera vez una guitarra al carrito
      guitarra.cantidad = 1
      carrito.value.push(guitarra)
    }else {
      guitarra.cantidad++
    }

  }

  const decrementarCantidad = (id) => {
    //encontrar el indice del producto dentro del carrito
    const index = carrito.value.findIndex(producto => producto.id === id)

    //si la cantidad del producto es menor o igual a uno 
    if(carrito.value[index].cantidad <= 1) return 

    //decrementar la cantidad del carrito
    carrito.value[index].cantidad--

  }

  const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)

    if(carrito.value[index].cantidad >= 10) return 

    carrito.value[index].cantidad++
  }

  const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id)
  }

  const vaciarCarrito = () => {
    carrito.value = []
  }

</script>

<template>
  <Headers
    :carrito="carrito"
    :guitarra="guitarra"
    @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
     />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">

      <Guitarra 
        v-for="guitarra in guitarras" 
        :guitarra="guitarra" 
        @agregar-carrito="agregarCarrito" 
      />

    </div>

  </main>

  <Footer />
</template>

<style scoped></style>
