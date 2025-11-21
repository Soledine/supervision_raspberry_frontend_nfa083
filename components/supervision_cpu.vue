<script setup>
  import { ref, onMounted} from 'vue';
  const cpuLoad = ref([]);
  import axios from "axios";


   onMounted(() => {
      axios.get("http://localhost:8080/api/mesuresCpu").then(reponse => {
      // ajouter les nouvelles valeurs
      reponse.data.forEach(val => cpuLoad.value.push(val))

      //console.log("CPU Load :", cpuLoad)
    })
    .catch(err => console.error(err))
  });


</script>
<template><p>charge Cpu: </p>
    <p>{{cpuLoad}}</p>


    <!--
      <ul>
        <li v-for="(load, index) in cpuLoad" :key="index">
          {{ load }}
        </li>
      </ul>
    -->
</template>

