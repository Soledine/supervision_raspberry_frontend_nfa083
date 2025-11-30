<script setup>
import { ref, onMounted, nextTick } from 'vue'
import axios from 'axios'
import { Chart } from 'chart.js/auto'

const chartCanvas = ref(null)

onMounted(async () => {
  try {

    let chartInstance = new Chart(chartCanvas.value.getContext('2d'), {
      type: 'line',
      data: {
        labels: [],
        datasets: [{
          label: 'Charge CPU',
          borderWidth: 2,
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

    function ajouterValeur(nouvelleValeur) {
      const index = chartInstance.data.labels.length + 1
      chartInstance.data.labels.push(`Mesure ${index}`)
      chartInstance.data.datasets[0].data.push(nouvelleValeur)
      chartInstance.update()
    }

    function sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }

    async function completerGrapheCpu() {
      while (true){
      //let reponse = await axios.get("http://localhost:8080/api/lastMesure")
      let reponse = await axios.get("/api/lastMesure")
      let mesureCpu = reponse.data.cpuLoad
      ajouterValeur(mesureCpu);
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
  <div style="height:300px;">
    <canvas ref="chartCanvas"></canvas>
  </div>
</template>
