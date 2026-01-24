<script setup>
import { ref, onMounted, nextTick } from 'vue'
import axios from 'axios'
import { Chart } from 'chart.js/auto'

const chartCanvas = ref(null)
let currentValue=ref(0)
let minValue=ref(0)
let maxValue=ref(0)


onMounted(async () => {
  try {

    let chartInstance = new Chart(chartCanvas.value.getContext('2d'), {
      type: 'line',
      data: {
        labels: [],
        datasets: [{
          label: 'Charge CPU',
          borderWidth: 2,
          borderColor:'#79c2d0',
          tension: 0.4,
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          y: {
            beginAtZero: true,
            //max:5
          }
        }
      }
    })

    function ajouterValeur(nouvelleValeur,time) {
      //const index = chartInstance.data.labels.length + 1
      chartInstance.data.labels.push(time)
      chartInstance.data.datasets[0].data.push(nouvelleValeur)
      currentValue.value=nouvelleValeur
      minValue.value=Math.min(...chartInstance.data.datasets[0].data)
      maxValue.value=Math.max(...chartInstance.data.datasets[0].data)
      chartInstance.update()
    }

    function sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }

    async function completerGrapheCpu() {
      while (true){
      //let reponse = await axios.get("http://localhost:8080/api/lastMesureCpu")
      let reponse = await axios.get("/serverMonitor/api/lastMesureCpu")
      let mesureCpu = reponse.data.cpuLoad
      let time = reponse.data.time
      ajouterValeur(mesureCpu,time);
      console.log(mesureCpu)
      await sleep(3000);
      }
    }

    completerGrapheCpu();
  } catch (error) {
    console.error("Erreur API :", error)
  }
})
</script>

<template>
  <div class="cpu-container">
    <div class="chart-wrapper">
      <canvas ref="chartCanvas"></canvas>
    </div>

    <div class="stats-wrapper">
      <h4>CPU (%)</h4>

      <div class="stat">
        <span class="label">Courant</span>
        <span class="value cpu">{{ currentValue }} %</span>
      </div>

      <div class="stat">
        <span class="label">Min</span>
        <span class="value">{{ minValue }} %</span>
      </div>

      <div class="stat">
        <span class="label">Max</span>
        <span class="value">{{ maxValue }} %</span>
      </div>
    </div>
  </div>
</template>

