<script setup>
import { ref, onMounted, nextTick } from 'vue'
import axios from 'axios'
import { Chart } from 'chart.js/auto'


const chartCanvas = ref(null)
let download=ref(0)
let upload=ref(0)
let nominterface=ref(0)


onMounted(async () => {
  try {

    let delaiMesure=3000
    let ancienneValeurDown=0
    let ancienneValeurUp=0

    let chartInstance = new Chart(chartCanvas.value.getContext('2d'), {
      type: 'line',
      data: {
        labels: [],
        datasets: [{
          label: 'Download',
          borderColor:'red',
          borderWidth: 2,
          tension: 0.4,
        },
        {
          label: 'Upload',
          borderColor:'yellow',
          borderWidth: 2,
          tension: 0.4,
        }
      ]
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

    function ajouterValeur(nom,downloadValue,uploadValue,time) {
      //const index = chartInstance.data.labels.length + 1
      console.log("uploadValue = "+uploadValue)
      console.log("downloadValue = "+downloadValue)
      chartInstance.data.labels.push(time)
      let nbValeurs = chartInstance.data.datasets[0].data.length;
      let debitDownload=0
      let debitUpload=0
      if(nbValeurs==0){
        chartInstance.data.datasets[0].data.push(0)
        chartInstance.data.datasets[1].data.push(0)
      }

      else {
        debitDownload = ((downloadValue-ancienneValeurDown)/(delaiMesure*1000)).toFixed(2);
        debitUpload = ((uploadValue-ancienneValeurUp)/(delaiMesure*1000)).toFixed(2);
        chartInstance.data.datasets[0].data.push(debitDownload)
        chartInstance.data.datasets[1].data.push(debitUpload)
      }
      ancienneValeurDown=downloadValue
      ancienneValeurUp=uploadValue

      console.log("debit up= "+debitDownload)
      console.log("debit down = "+debitUpload)
      nominterface.value=nom
      download.value=debitDownload
      upload.value=debitUpload
      chartInstance.update()
    }

    function sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }

    async function completerGrapheMemoire() {
      while (true){
      //let reponse = await axios.get("http://localhost:8080/api/lastMesureMoire")
      let reponse = await axios.get("/serverMonitor/api/lastMesureNetwork")
      let nom = reponse.data.name;
      let download = reponse.data.bytesReceived;
      let upload = reponse.data.bytesSent;
      let time = reponse.data.time;
      ajouterValeur(nom,download,upload,time);
      await sleep(delaiMesure);
      }
    }

    completerGrapheMemoire();
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
      <h4>{{ nominterface }}</h4>

      <div class="stat">
        <span class="label">Download</span>
        <span class="value download">{{ download }} MB/s</span>
      </div>

      <div class="stat">
        <span class="label">Upload</span>
        <span class="value upload">{{ upload }} MB/s</span>
      </div>

      
    </div>
  </div>
</template>

