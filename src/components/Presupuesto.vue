<script setup>
    import { ref } from 'vue';
    import Alerta from './Alerta.vue'

    const emit = defineEmits(['confirmar-presupuesto'])

    const presupuesto = ref(0)
    const isError = ref('')

    const definirPresupuesto = () => {
        if(presupuesto.value <= 0 || presupuesto.value == ''){
            isError.value = 'Presupuesto no valido'

            setTimeout(() => {
                isError.value = ''
            }, 2000)

            return
        }
        emit('confirmar-presupuesto', presupuesto.value)
    }

</script>

<template>
    <form 
        class="presupuesto"
        @submit.prevent="definirPresupuesto"
    >

        <Alerta v-if="isError">
            {{ isError }}
        </Alerta>

        <div class="campo">
            <label for="nuevo-presupuesto">Definir presupuesto</label>
            <input 
                type="number" 
                id="nuevo-presupuesto"
                placeholder="Agrega tu presupuesto"
                v-model.number="presupuesto"
            >
        </div>

        <input type="submit" value="Definir presupuesto">
    </form>
</template>

<style scoped>
    .presupuesto{
        width: 100%;
    }
    .campo{
        display: grid;
        gap: 2rem;
    }
    .presupuesto label{
        font-size: 2.8rem;
        text-align: center;
        color: var(--azul);
    }
    .presupuesto input[type="number"]{
        background-color: var(--gris-claro);
        border-radius: 1rem;
        padding: 1rem;
        border: none;
        font-size: 2.2rem;
        text-align: center;
    }
    .presupuesto input[type="submit"]{
        background-color: var(--azul);
        border: none;
        padding: 1rem;
        text-align: center;
        font-size: 2rem;
        margin-top: 2rem;
        color: var(--blanco);
        font-weight: 900;
        width: 100%;
        transition: 300ms ease;
    }
    .presupuesto input[type="submit"]:hover{
        cursor: pointer;
        background-color: #1048a4;
    }
</style>