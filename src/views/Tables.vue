<template>
  <div class="container mx-auto px-4 py-8">
    <div class="flex justify-between items-center mb-4">
      <div class="flex items-center space-x-4">
        <label for="periodo" class="text-sm font-medium text-gray-700">Periodo:</label>
        <select id="periodo" v-model="filtroPeriodo" class="border border-gray-300 rounded-md px-2 py-1 w-32">
            <option value="">Todos</option>
            <option v-for="periodo in periodosUnicos" 
            :key="periodo" :value="periodo">{{ periodo }}</option>
        </select>

      </div>
      <div class="flex items-center space-x-4">
        <label for="facultad" class="text-sm font-medium text-gray-700">Facultad:</label>
        <select id="facultad" v-model="filtroFacultad" class="border border-gray-300 rounded-md px-2 py-1 w-32">
              <option value="">Todos</option>
              <option v-for="facultad in facultadesUnicos" :key="facultad" :value="facultad">{{ facultad }}</option>
       </select>
      </div>
      <div class="flex items-center space-x-4">
        <label for="programa" class="text-sm font-medium text-gray-700">Programa:</label>
        <select id="programa" v-model="filtroPrograma" class="border border-gray-300 rounded-md px-2 py-1 w-32">
              <option value="">Todos</option>
              <option v-for="programa in programasUnicos" :key="programa" :value="programa">{{ programa }}</option>
        </select>
      </div>
      
    </div>
    <table class="w-full table-auto">
      <thead class="bg-gray-200">
        <tr>
          <th class="px-4 py-2">Periodo</th>
          <th class="px-4 py-2">Programa</th>
          <th class="px-4 py-2">Total Matrícula</th>
          <th class="px-4 py-2">Sexo Femenino</th>
          <th class="px-4 py-2">Sexo Masculino</th>
          <th class="px-4 py-2">Estrato 1</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(fila, index) in datosFiltrados" :key="index">
          <td class="border px-4 py-2">{{ fila.periodo }}</td>
          <td class="border px-4 py-2">{{ fila.programa }}</td>
          <td class="border px-4 py-2">{{ fila.total_matricula }}</td>
          <td class="border px-4 py-2">{{ fila.sexo_feme }}</td>
          <td class="border px-4 py-2">{{ fila.sexo_masc }}</td>
          <td class="border px-4 py-2">{{ fila.estrato_1 }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      datos: [],
      filtroPeriodo: '',
      filtroFacultad: '',
      filtroPrograma: '',
    };
  },
  async created() {
  try {
    const response = await axios.get('https://www.datos.gov.co/resource/r86y-229a.json');
    this.datos = response.data;
  } catch (error) {
    console.error('Error al obtener los datos:', error);
  }
},
  computed: {
  datosFiltrados() {
       let filtro = this.datos;
       if (this.filtroPeriodo) {
         filtro = filtro.filter(fila => fila.periodo === this.filtroPeriodo);
       }
       if (this.filtroFacultad) {
         filtro = filtro.filter(fila => fila.facultad === this.filtroFacultad);
       }
       if (this.filtroPrograma) {
         filtro = filtro.filter(fila => fila.programa === this.filtroPrograma);
       }
       return filtro;
  },
  periodosUnicos() {
  return [...new Set(this.datos.map(fila => fila.periodo))];
  },
  facultadesUnicos() {
  return [...new Set(this.datos.map(fila => fila.facultad))];
  },
  programasUnicos() {
  return [...new Set(this.datos.map(fila => fila.programa))];
},
  },
};
</script>



