<template>
  <div class="app-container">
    <div class="column" v-for="column in columns" :key="column">
      <Column v-bind:column="column"/>
    </div>
  </div>
</template>

<script>

import Column from './components/Column.vue';
import axios from 'axios';

export default {
  name: 'App',
  components: {
    Column,
  },
  data: function() {
    return {
      columns: [1, 2, 3]
    }
  },
  mounted() {
    axios.get('http://localhost:8000/api/cards').then(res => {
      this.columns = res.data;
      console.log(this.columns);
    });
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
