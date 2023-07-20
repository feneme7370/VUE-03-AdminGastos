<script setup>
    import imagen from '../assets/img/grafico.jpg'
    import {formatearCantidad} from '../helpers/index.js'

    const props = defineProps({
        presupuesto : {type: Number, deep: true},
        disponible : {type: Number, deep: true}
    })
</script>

<template>
    <div class="dos-columnas">
        <div class="contenedor-grafico">
            <img :src="imagen" alt="">
        </div>
        <div class="contenedor-presupuesto">
            <button 
                class="reset-app"
            >Resetear App</button>
            <p><span>Presupuesto:</span>  {{ formatearCantidad(presupuesto)}}</p>
            <p><span>Disponible:</span>  {{formatearCantidad(disponible)}}</p>
            <p><span>Gastado:</span> 0</p>
        </div>
    </div>
</template>

<style scoped>
    .dos-columnas{
        display: flex;
        flex-direction: column;
    }
    .dos-columnas > :first-child{
        margin-bottom: 3rem;
    }
    @media (min-width: 768px) {
        .dos-columnas{
            flex-direction: row;
            gap: 4rem;
            align-items: center;
        }
        .dos-columnas > :first-child{
            margin-bottom: 0;
        }
    }

    .contenedor-presupuesto{
        width: 100%;
    }
    .contenedor-presupuesto p{
        font-size: 2.4rem;
        text-align: center;
        color: var(--gris-oscuro);
    }
    @media (min-width: 768px){
        .contenedor-presupuesto p{
            text-align: left;
        }
    }
    .contenedor-presupuesto span{
        font-weight: 900;
        color: var(--azul);
    }
    .reset-app{
        background-color: #de2929;
        color: var(--blanco);
        font-weight: 900;
        text-transform: uppercase;
        border-radius: 1rem;
        transition: 300ms;
        border: none;
        padding: 1rem;
        width: 100%;
    }
    .reset-app:hover{
        cursor: pointer;
        background-color: #d22020;
    }
</style>