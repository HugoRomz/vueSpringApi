<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'
import Swal from 'sweetalert2'

const data = ref(null)
let isEditing = ref(false)

const url = 'http://localhost:8080/api/v1/estudiante'

let estudiante = ref({
  nombre: '',
  direccion: '',
  telefono: ''
})

const editEstudiante = (estudianteData) => {
  estudiante.value = { ...estudianteData }
  isEditing.value = true
}

const getEstudiantes = async () => {
  try {
    const response = await axios.get(url)
    data.value = response.data
  } catch (error) {
    Swal.fire({
      title: 'Error',
      text: 'Ha ocurrido un error al guardar el estudiante',
      icon: 'error'
    })
  }
}

const guardarEstudiante = async () => {
  try {
    if (isEditing.value) {
      await axios.put(`${url}/${estudiante.value.id}`, estudiante.value)
      isEditing.value = false
      Swal.fire({
        title: 'Operacion Completa',
        text: 'Estudiante editado correctamente',
        icon: 'success'
      })
    } else {
      await axios.post(url, estudiante.value)
      Swal.fire({
        title: 'Operacion Completa',
        text: 'Estudiante agregado correctamente',
        icon: 'success'
      })
    }
    limpiar()
    getEstudiantes()
  } catch (error) {
    Swal.fire({
      title: 'Error',
      text: 'Ha ocurrido un error al guardar el estudiante',
      icon: 'error'
    })
  }
}

const deleteEstudiante = async (id) => {
  try {
    Swal.fire({
      title: '¿Estás seguro?',
      text: 'Una vez eliminado, no podrás recuperar este estudiante',
      icon: 'warning',
      showCancelButton: true,
      confirmButtonColor: '#3085d6',
      cancelButtonColor: '#d33',
      confirmButtonText: 'Sí, eliminarlo'
    }).then(async (result) => {
      if (result.isConfirmed) {
        await axios.delete(`${url}/${id}`)
        Swal.fire('Eliminado', 'El estudiante ha sido eliminado', 'success')
        getEstudiantes()
      }
    })
  } catch (error) {
    Swal.fire({
      title: 'Error',
      text: 'Ha ocurrido un error al guardar el estudiante',
      icon: 'error'
    })
  }
}

const limpiar = () => {
  estudiante.value = { id: '', nombre: '', direccion: '', telefono: '' }
}

onMounted(getEstudiantes)
</script>

<template>
  <div class="max-w-7xl mx-auto py-6 sm:px-6 lg:px-8">
    <div class="m-5">
      <h2 class="mt-4 font-bold text-3xl uppercase text-emerald-500 text-center">
        Crud Estudiante
      </h2>
    </div>
    <div
      class="bg-white shadow-lg overflow-hidden sm:rounded-lg mb-4 border border-collapse border-gray-300"
    >
      <div class="p-6">
        <form @submit.prevent="guardarEstudiante" class="space-y-4">
          <input type="hidden" v-model="estudiante.id" />
          <div class="flex flex-col sm:flex-row sm:space-x-4">
            <div class="flex-1">
              <label for="nombre" class="mb-2 font-bold text-lg">Nombre:</label>
              <input
                type="text"
                id="nombre"
                v-model="estudiante.nombre"
                required
                class="p-2 border rounded shadow-sm w-full"
              />
            </div>
            <div class="flex-1">
              <label for="telefono" class="mb-2 font-bold text-lg">Teléfono:</label>
              <input
                type="text"
                id="telefono"
                v-model="estudiante.telefono"
                required
                class="p-2 border rounded shadow-sm w-full"
              />
            </div>
          </div>
          <div class="flex flex-col">
            <label for="direccion" class="mb-2 font-bold text-lg">Dirección:</label>
            <input
              type="text"
              id="direccion"
              v-model="estudiante.direccion"
              required
              class="p-2 border rounded shadow-sm w-full"
            />
          </div>
          <div class="flex justify-end">
            <button
              type="submit"
              class="w-full p-2 mt-4 bg-green-500 text-white rounded hover:bg-green-600"
            >
              Guardar
            </button>
            <button
              type="button"
              class="w-full p-2 mt-4 bg-blue-600 text-white rounded hover:bg-blue-700 ml-2"
              @click="limpiar"
            >
              Limpiar
            </button>
          </div>
        </form>
      </div>
    </div>

    <div
      class="bg-white shadow-md overflow-hidden sm:rounded-lg border border-collapse border-gray-300"
    >
      <div class="p-6">
        <table
          class="min-w-full bg-white shadow-md rounded-lg border-collapse border border-slate-500"
        >
          <thead class="bg-blue-gray-100 text-gray-700">
            <tr>
              <th class="py-3 px-4 text-left border border-slate-300">ID</th>
              <th class="py-3 px-4 text-left border border-slate-300">Nombre</th>
              <th class="py-3 px-4 text-left border border-slate-300">Dirección</th>
              <th class="py-3 px-4 text-left border border-slate-300">Teléfono</th>
              <th class="py-3 px-4 text-left border border-slate-300">Acciones</th>
            </tr>
          </thead>
          <tbody class="text-blue-gray-900">
            <tr v-for="dataItem in data" :key="dataItem.id">
              <td class="py-3 px-4 border border-slate-300">{{ dataItem.id }}</td>
              <td class="py-3 px-4 border border-slate-300">{{ dataItem.nombre }}</td>
              <td class="py-3 px-4 border border-slate-300">{{ dataItem.direccion }}</td>
              <td class="py-3 px-4 border border-slate-300">{{ dataItem.telefono }}</td>
              <td class="py-3 px-4 border border-slate-300">
                <button
                  class="bg-yellow-500 hover:bg-yellow-500 text-white font-bold text-[15px] py-1 px-2 rounded mr-2"
                  @click="editEstudiante(dataItem)"
                >
                  Editar
                </button>
                <button
                  class="bg-red-500 hover:bg-red-700 text-white font-bold text-[15px] py-1 px-2 rounded"
                  @click="deleteEstudiante(dataItem.id)"
                >
                  Eliminar
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
