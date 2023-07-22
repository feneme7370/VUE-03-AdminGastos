<script setup>
  import { ref, reactive, watch, onMounted, computed } from 'vue';
  import {generarId, formatearFecha} from './helpers/index.js'
  import Presupuesto from './components/Presupuesto.vue';
  import ControlPresupuesto from './components/ControlPresupuesto.vue';
  import Modal from './components/Modal.vue'
  import TarjetaGasto from './components/TarjetaGasto.vue'
  import Filtro from './components/Filtro.vue'

  import iconoNuevoGasto from './assets/img/nuevo-gasto.svg'

  // las enviamos a ControlPresupuesto
  const presupuesto = ref(0)
  const gastado = ref(0)
  const disponible = ref(presupuesto.value-gastado.value)
  const filtro = ref('')
  // acumular gastos
  const gastos = ref([])

  const modal = reactive({
    mostrar: false,
    animar: false
  })
  
  // actualizar array de gastos
  watch(gastos, () => {
    const totalGastado = gastos.value.reduce((total, gasto) => Number(gasto.cantidad) + Number(total), 0)
    gastado.value = Number(totalGastado)
    disponible.value = Number(presupuesto.value) - totalGastado
    localStorage.setItem('gastos', JSON.stringify(gastos.value)) 
    }, {
      deep: true
  })

  // limpiar modal al accionar
  watch(modal, () => {
    if(!modal.mostrar){
      limpiarGasto()
    }  
  }, {
    deep: true
  })

  // almacenar presupuesto en local storage
  watch(presupuesto, () => {
    localStorage.setItem('presupuesto', presupuesto.value)
  })

  // inicializar variable gasto
  const gasto = reactive({
    nombre: '',
    cantidad: '',
    categoria: '',
    id: null,
    fecha: Date.now(),
  })

  // entiendo que se actualiza cada vez que pasa algo
  const gastosFiltrados = computed(() => {
    if(filtro.value){
      return gastos.value.filter(gasto => gasto.categoria === filtro.value)
    }
    return gastos.value
  })

  // precargar datos del local storage
  onMounted( () => {
    const cargarLocalGastos = localStorage.getItem('gastos')
    if(cargarLocalGastos){
      gastos.value = JSON.parse(cargarLocalGastos)
    }
    const cargarLocalPresupuesto = localStorage.getItem('presupuesto')
    if(cargarLocalPresupuesto){
      presupuesto.value = Number(cargarLocalPresupuesto)
    }
  })

  // viene de Presupuesto
  const confirmarPresupuesto = (valor) => {
    presupuesto.value = valor
    disponible.value = presupuesto.value - gastado.value    
  }

  // mostrar el modal
  const mostrarModal = () => {
    modal.mostrar = true
    setTimeout(() => {
      modal.animar = true
    },300)
  }

  // cerrar modal, viene de Modal
  const cerrarModal = () => {
    modal.animar = false
    setTimeout(()=>{
      modal.mostrar = false
    },300)
  }
 
  // limpiar objeto gasto
  const limpiarGasto = () => {
    Object.assign(gasto, {
        nombre: '',
        cantidad: '',
        categoria: '',
        id: null,
        fecha: Date.now()
      }
    )
  }

  // almacenar gasto
  const guardarGasto = () => {
    if(gasto.id){
      // obtengo indice del id, y pego la copia editada en esa posicion
      const {id} = gasto
      const i = gastos.value.findIndex( gasto => gasto.id === id)
      gastos.value[i] = {...gasto}
    }else{
      // agrego una copia al array
      gastos.value.push({
        ...gasto,
        id: generarId(),
      })
    }
    limpiarGasto()
    cerrarModal()
  }

  // seleccionar gasto
  const seleccionarGasto = (id) => {
    const gastoEditar = gastos.value.filter(gasto => gasto.id === id)[0]
    Object.assign(gasto, gastoEditar)
    mostrarModal()
  }

  // eliminar gasto
  const eliminarGasto = (id) => {
    if(confirm('Eliminar?')){
      gastos.value = gastos.value.filter(gasto => gasto.id !== id)
      cerrarModal()
    }
  }

  // resetear app
  const resetApp = () => {
    if(confirm('Deseas resetear la app?')){
      gastos.value = []
      presupuesto.value = 0
    }
  }
</script>

//la clase fijar, reemplaza el ternario, si mostrar.modal existe, agrega fijar, te ahorra el si no existe
<template>
  <div
    :class="{fijar: modal.mostrar}"
  >
    <header>
      <h1>Planificador de gastos</h1>

      <div class="contenedor contenedor-header sombra">
        <Presupuesto v-if="presupuesto === 0"
          @confirmar-presupuesto="confirmarPresupuesto"
        />
        <ControlPresupuesto v-else
          @reset-app="resetApp"
          :presupuesto="presupuesto"
          :disponible="disponible"
          :gastado="gastado"
        />
      </div>
    </header>

    <main v-if="presupuesto > 0">
      <Filtro
        v-model:filtro="filtro"
      />
      <div 
        class="crear-gasto"
        @click="mostrarModal"
      >
        <img :src="iconoNuevoGasto" alt="icono nuevo gasto">
      </div>

      <Modal
        v-if="modal.mostrar"
        @cerrar-modal="cerrarModal"
        @guardar-gasto="guardarGasto"
        @eliminar-gasto="eliminarGasto"
        :modal="modal"
        :disponible="disponible"
        :id="gasto.id"
        v-model:nombre="gasto.nombre"
        v-model:cantidad="gasto.cantidad"
        v-model:categoria="gasto.categoria"
      />
    </main>

    <div class="listado-gastos contenedor">
      <h2>{{ gastosFiltrados.length > 0 ? 'Gastos' : 'No hay gastos' }}</h2>
        <TarjetaGasto
          v-for="gasto in gastosFiltrados"
          @seleccionar-gasto="seleccionarGasto"
          :key="gasto.id"
          :gasto="gasto"
        />
    </div>

  </div>
</template>

<style>
  :root{
    --azul: #3b82f6;
    --blanco: #fff;
    --gris-claro: #f5f5f5;
    --gris: #94a3b8;
    --gris-oscuro: #64748b;
    --negro: #000;
  }
  html{
    font-size: 62.5%;
    box-sizing: border-box;
  }
  *,*:before,*:after{
    box-sizing: inherit;
  }
  body{
    font-size: 1.6rem;
    font-family: "Lato", sans-serif;
    background-color: var(--gris-claro);
  }
  h1{
    font-size: 4rem;
  }
  h2{
    font-size: 3rem;
  }
  .fijar{
    overflow: hidden;
    height: 100vh;
  }
  header{
    background-color: var(--azul);
  }
  header h1{
    padding: 3rem 0;
    margin: 0;
    color: var(--blanco);
    text-align: center;
  }
  .contenedor{
    width: 90%;
    max-width: 80rem;
    margin: 0 auto;
  }
  .contenedor-header{
    margin-top: -5rem;
    transform: translateY(5rem);
    padding: 5rem;
  }
  .sombra{
    box-shadow: 0px 10px 15px -3px rgba(0,0,0,0.1);
    background-color: var(--blanco);
    border-radius: 1.2rem;
    padding: 5rem;
  }
  .crear-gasto{
    position: fixed;
    bottom: 5rem;
    right: 5rem;
  }
  .crear-gasto img{
    width: 5rem;
    cursor: pointer;
  }
  .listado-gastos{
    margin-top: 7rem;
  }
  .listado-gastos h2{
    font-weight: 900;
    color: var(--gris-oscuro);
  }
</style>