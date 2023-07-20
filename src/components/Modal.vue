<script setup>
    import cerrarModalLogo from '../assets/img/cerrar.svg'

    const emit = defineEmits(['cerrar-modal','update:nombre','update:categoria','update:cantidad'])

    const props = defineProps({
        modal:{type: Object,required: true},
        nombre:{type: String,required: true},
        cantidad:{type: [String, Number],required: true},
        categoria:{type: String,required: true},
    })

</script>

//contenedor-formulario sirve para la transition del modal con sus clases
<template>
    <div class="modal">
        <div 
            class="cerrar-modal" 
            @click="$emit('cerrar-modal')"
        >
            <img :src="cerrarModalLogo" alt="">
        </div>

        <div
            class="contenedor contenedor-formulario"
            :class="[modal.animar ? 'animar' : 'cerrar']"
        >
            <form class="nuevo-gasto">
                <legend>Agregar gasto</legend>

                <div class="campo">
                    <label for="nombre">Nombre gasto:</label>
                    <input 
                        type="text"
                        id="nombre"
                        placeholder="Agrega el nombre del gasto"
                        :value="nombre"
                        @input="$emit('update:nombre', $event.target.value)"
                    >
                </div>
                <div class="campo">
                    <label for="cantidad">Cantidad gasto:</label>
                    <input 
                        type="number"
                        id="cantidad"
                        placeholder="Agrega el cantidad del gasto"
                        :value="cantidad"
                        @input="$emit('update:cantidad', $event.target.value)"
                    >
                </div>
                <div class="campo">
                    <label for="categoria">Categoria gasto:</label>
                    <select 
                        id="categoria"
                        :value="categoria"
                        @input="$emit('update:categoria', $event.target.value)"
                    >
                        <option value="">-- Seleccionar --</option>
                        <option value="ahorro">Ahorro</option>
                        <option value="comida">Comida</option>
                        <option value="casa">Casa</option>
                        <option value="gastos">Gastos varios</option>
                        <option value="ocio">Ocio</option>
                        <option value="salud">Salud</option>
                        <option value="suscripciones">Suscripciones</option>
                    </select>

                </div>

                <input type="submit" value="Agregar gasto">
            </form>
        </div>
    </div>
</template>

<style scoped>
    .modal{
        position: absolute;
        background-color: rgb(0 0 0 / 0.9);
        top: 0;
        right: 0;
        left: 0;
        bottom: 0;
    }
    .contenedor-formulario{
        transition-property: all;
        transition-duration: 300ms;
        transition-timing-function: ease-in-out;
        opacity: 0;
    }
    .contenedor-formulario.animar{
        opacity: 1;
    }
    .contenedor-formulario.cerrar{
        opacity: 0;
    }
    .cerrar-modal{
        position: absolute;
        right: 3rem;
        top: 3rem;
    }
    .cerrar-modal img{
        width: 3rem;
        cursor: pointer;
    }
    .nuevo-gasto{
        margin: 10rem auto 0 auto;
        display: grid;
        gap: 2rem;
    }
    .nuevo-gasto legend{
        text-align: center;
        color: var(--blanco);
        font-size: 3rem;
        font-weight: 700;
    }
    .campo{
        display: grid;
        gap: 2rem;
    }
    .nuevo-gasto input, .nuevo-gasto select{
        background-color: var(--gris-claro);
        border-radius: 1rem;
        padding: 1rem;
        border: none;
        font-size: 2.2rem;
    }
    .nuevo-gasto label{
        color: var(--blanco);
        font-size: 3rem;
    }
    .nuevo-gasto input[type="submit"]{
        font-weight: 700;
        background-color: var(--azul);
        color: var(--blanco);
        cursor: pointer;
    }
</style>