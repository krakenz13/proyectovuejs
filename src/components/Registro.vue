<script setup>
import { ref, reactive, onMounted } from 'vue'
import api from '@/api.js'

const esNuevo = ref(true)
const indice = ref(0)

const tiposDocumentos = reactive([
    { id: 1, nombre: 'Cédula' },
    { id: 2, nombre: 'Tarjeta' }
])

const personaState = reactive({
    persona: {
        tipoDocumento: 0,
        numeroDocumento: 0,
        nombre: '',
        edadDocumento: 0
    }
})

const personasState = reactive({
    personas: []
})

onMounted(() => {
    obtenerListadoPersonas()
})

const obtenerListadoPersonas = () => {
    api.get(
            'personas'
        ).then( (response) => {
            console.log(response)

            response.data.map(data => {
                personasState.personas.push({ 
                    tipoDocumento: data.tipo_documento,
                    numeroDocumento: data.numero_documento,
                    nombre: data.nombre,
                    edadDocumento: data.edad        
                })                
            })

        })
}

const guardarPersona = () => {
    if (esNuevo.value) {
        api.post(
            'personas',
            {
                "tipo_documento": personaState.persona.tipoDocumento.toString(),
                "numero_documento": personaState.persona.numeroDocumento.toString(),
                "nombre": personaState.persona.nombre.toString(),
                "edad": personaState.persona.edadDocumento.toString()
            }
        ).then( (response) => {
            console.log(response)
        })

        personasState.personas.push({ ...personaState.persona })

    } else {
        personasState.personas[indice.value].nombre = personaState.persona.nombre
        personasState.personas[indice.value].tipoDocumento = personaState.persona.tipoDocumento
        personasState.personas[indice.value].numeroDocumento = personaState.persona.numeroDocumento
        personasState.personas[indice.value].edadDocumento = personaState.persona.edadDocumento
    }
    console.log(personaState.personas)

    limpiarCampos()
}

const limpiarCampos = () => {
    personaState.persona.nombre = ''
    personaState.persona.tipoDocumento = 0
    personaState.persona.numeroDocumento = 0
    personaState.persona.edadDocumento = 0

    esNuevo.value = true

}

const modificarPersona = (i) => {
    indice.value = i
    personaState.persona.nombre = personasState.personas[indice.value].nombre
    personaState.persona.tipoDocumento = personasState.personas[indice.value].tipoDocumento
    personaState.persona.numeroDocumento = personasState.personas[indice.value].numeroDocumento
    personaState.persona.edadDocumento = personasState.personas[indice.value].edadDocumento
    esNuevo.value = false
}

const eliminarPersona = (i) => {
    personasState.personas.splice(i, 1)
    limpiarCampos()
}

</script>

<template>
    <div class="hello">
        <div class="dashboard">
            <label for="nombre">nombre y apellido:</label>
            <br>
            <input class="cambio1" type="text" v-model="personaState.persona.nombre">
            <br>
            <br>
            <label for="numeroDocumento">numero Documento:</label>
            <br>
            <input class="cambio1" type="text" v-model="personaState.persona.numeroDocumento">
            <br>
            <br>
            <label for="edadDocumento">edad:</label>
            <br>
            <input class="cambio1" v-model="personaState.persona.edadDocumento" type="number">
            <br>
            <br>
            <label for="tipoDocumento">tipo Documento:</label>
            <br>

            <select class="cambio1" v-model="personaState.persona.tipoDocumento">
                <option selected disabled value="0">seleccione tipo de Documento</option>
                <option v-for="(td) in tiposDocumentos" v-bind:key="td.id">{{ td.nombre }}</option>

            </select>
            <br>
            <br>

            <button @click="guardarPersona()">Guardar</button>
            <br>
            <br>
            <br>
            <br>
            <hr>
            <table class="tablita" border="1">
                <tr>
                    <th width="300px">tipo documento</th>
                    <th width="300px">numero documento</th>
                    <th width="300px">nombre y apellido</th>
                    <th width="100px">edad</th>
                    <th width="200px">acción</th>
                </tr>

                <tr v-for="(p, i) in personasState.personas" v-bind:key="p" v-bind:index="i">
                    <td>{{ p.tipoDocumento }}</td>
                    <td>{{ p.numeroDocumento }}</td>
                    <td>{{ p.nombre }}</td>
                    <td>{{ p.edadDocumento }}</td>
                    <td>
                        <button v-on:click="modificarPersona(i)">Modificar</button>
                        <button v-on:click="eliminarPersona(i)">Eliminar</button>
                    </td>

                </tr>
            </table>
        </div>


    </div>

</template>

<style>
.dashboard {
    justify-content: center;
    text-align: center;
    padding: 10px;
    border-style: groove;
    border-width: 2px;
    border-color: #10539F;
}

.tablita {
    justify-content: end;
    background-color: aliceblue;
}

.cambio1 {
    border-style: inset;
    border-radius: 20px;
    padding: 8px;


}

label {
    text-transform: uppercase;
    font-family: Georgia, 'Times New Roman', Times, serif;


}

button {
    background-color: #10539F;
    font-family: Georgia, 'Times New Roman', Times, serif;
    text-transform: uppercase;
    border-radius: 20px;
    border-width: 3px;
    padding: 5px;
    text-transform: uppercase;

}

body {
    background-image: url('img/fondo.jpg');
    width: 100%;
}

.btn2 {
    display: block;
    margin-left: auto;
    margin-top: 30px;
    
    
}

.btn{
    display: block;
    padding: 30px;
    
    

}
</style>
