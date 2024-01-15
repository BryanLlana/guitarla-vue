<script setup>
  import { ref , onMounted, watch } from 'vue'
  import { db } from './data/guitarras'
  import Header from './components/Header.vue'
  import Guitarra from './components/Guitarra.vue'
  import Footer from './components/Footer.vue'
  
  const guitarras = ref([])
  const carrito = ref([])
  const guitarra = ref({})

  onMounted(() => {
    guitarras.value = db
    guitarra.value = db[3]

    const carritoLS = localStorage.getItem('carrito')
    if (carritoLS) {
      carrito.value = JSON.parse(carritoLS)
    }
  }) 

  watch(carrito, () => {
    guardarLocalStorage()
  }, {
    deep: true
  })

  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
  }

  const agregarCarrito = (guitarra) => {
    const productoExiste = carrito.value.findIndex(producto => producto.id === guitarra.id)
    if (productoExiste >= 0) {
      carrito.value[productoExiste].cantidad++
    } else {
      guitarra.cantidad = 1
      carrito.value.push(guitarra)
    }
  }

  const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if (carrito.value[index].cantidad < 5) {
      carrito.value[index].cantidad++
    }
  }

  const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if (carrito.value[index].cantidad > 1) {
      carrito.value[index].cantidad--
    }
  }

  const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id)
  }

  const vaciarCarrito = () => {
    carrito.value = []
  }
</script>

<template>
  <Header 
    v-bind:carrito="carrito"
    v-bind:guitarra="guitarra"
    v-on:incrementar-cantidad="incrementarCantidad"
    v-on:decrementar-cantidad="decrementarCantidad"
    v-on:eliminar-producto="eliminarProducto"
    v-on:vaciar-carrito="vaciarCarrito"
    v-on:agregar-carrito="agregarCarrito"
  />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>
    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        v-bind:guitarra="guitarra"
        v-on:agregar-carrito="agregarCarrito"
      />
    </div>
  </main>

  <Footer />
</template>