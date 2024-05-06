<template>
  <div class="container mx-auto px-4 py-8">
      <div class="flex justify-between items-center mb-4">
          <div class="flex items-center space-x-4">
              <label for="periodo" class="text-sm font-medium text-gray-700"
                  >Periodo:</label
              >
              <select
                  id="periodo"
                  v-model="filtroPeriodo"
                  class="border border-gray-300 rounded-md px-2 py-1 w-32"
              >
                  
                  <option
                      v-for="periodo in periodosUnicos"
                      :key="periodo"
                      :value="periodo"
                  >
                      {{ periodo }}
                  </option>
              </select>
          </div>
          <div class="flex items-center space-x-4">
              <label for="programa" class="text-sm font-medium text-gray-700"
                  >Genero:</label
              >
              <select
                  id="programa"
                  v-model="filtroTipo"
                  class="border border-gray-300 rounded-md px-2 py-1 w-32"
              >
                  <option value="todos">Todos</option>
                  <option value="mujeres">Mujeres</option>
                  <option value="hombres">Hombres</option>
              </select>
          </div>
          <button
              @click="filtrar"
              class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600"
          >
              Buscar
          </button>
      </div>
      <div>
          <canvas id="myChart"></canvas>
      </div>
  </div>
</template>

<script>
import axios from 'axios'
import Chart from 'chart.js/auto';
export default {
  data() {
      return {
          datos: [],
          items: [],
          filtroPeriodo: '2017-1',
          filtroTipo: 'todos',
          myChart: null,
      }
  },
  async created() {
      try {
          const response = await axios.get(
              'https://www.datos.gov.co/resource/r86y-229a.json'
          )
          this.datos = response.data
      } catch (error) {
          console.error('Error al obtener los datos:', error)
      }
  },
  computed: {
      datosFiltrados() {
          let filtro = this.datos
          if (this.filtroPeriodo) {
              filtro = filtro.filter(
                  (fila) => fila.periodo === this.filtroPeriodo
              )
          }
          return filtro
      },
      periodosUnicos() {
          return [...new Set(this.datos.map((fila) => fila.periodo))]
      },
  },
  methods: {
      filtrar() {
          let url = ' https://www.datos.gov.co/resource/r86y-229a.json'
          if (this.filtroPeriodo !== '') {
              url += `?periodo=${encodeURIComponent(this.filtroPeriodo)}`
          }

          axios
              .get(url)
              .then((result) => {
                  this.items = result.data
                  this.renderChart()
              })
              .catch((error) => {
                  alert(error.message)
              })
      },
      filterChartData() {
          if (this.filtroTipo === 'todos') {
              return this.items
          } else if (this.filtroTipo === 'hombres') {
              return this.items.filter((item) => item.sexo_masc !== '')
          } else if (this.filtroTipo === 'mujeres') {
              return this.items.filter((item) => item.sexo_feme !== '')
          }
      },
      destroyChart() {
          if (this.myChart) {
              this.myChart.destroy()
              this.myChart = null
          }
      },
      renderChart() {
          if (this.myChart) {
              this.destroyChart()
          }

          const ctx = document.getElementById('myChart').getContext('2d')
          const filteredData = this.filterChartData()

          this.myChart = new Chart(ctx, {
              type: 'bar',
              data: {
                  labels: filteredData.map((item) => item.programa),
                  datasets: [
                      {
                          label:
                              this.filtroTipo === 'todos'
                                  ? 'Total Matriculados'
                                  : `Total ${this.filtroTipo}`,
                          data: filteredData.map((item) => {
                              if (this.filtroTipo === 'todos') {
                                  return item.total_matricula
                              } else if (this.filtroTipo === 'hombres') {
                                  return item.sexo_masc
                              } else if (this.filtroTipo === 'mujeres') {
                                  return item.sexo_feme
                              }
                          }),
                          backgroundColor: 'rgba(48, 7, 87, 0.2)',
                          borderColor: 'rgba(48, 7, 87, 1)',
                          borderWidth: 1,
                      },
                  ],
              },
              options: {
                  scales: {
                      y: {
                          beginAtZero: true,
                      },
                  },
              },
          })
      },
  },
}
</script>