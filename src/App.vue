<template>
  <div class="app-container">
    <div class="column" v-for="column in columns" :key="column">
      <Column v-bind:column="column" v-bind:handleDeleteColumn="handleDeleteColumn" v-on:delete-column="handleDeleteColumn"/>
    </div>
    <button v-on:click="handleAddColumn">Add Column</button>
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
      columns: []
    }
  },
  methods: {
    handleDeleteColumn(id) {
      this.columns = this.columns.filter(column => {
        console.log(column);
        console.log(id);
        return column.id !== id
      });
    },
    async handleAddColumn() {
      try {
        const response = await axios.post('http://localhost:8000/api/columns', {
          title: 'New Column',
        });
        this.columns = [...this.columns, response.data];
      } catch (err) {
        console.error(err);
      }
    },
  },
  mounted() {
    try {
      axios.get('http://localhost:8000/api/cards').then(res => {
      this.columns = res.data;
      console.log(this.columns);
    });
    }
    catch (err) {
      console.log(err);
    }
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

.app-container {
  display: flex;
}

.column {
  border: 2px solid red;
  padding: 10px;
}
</style>
