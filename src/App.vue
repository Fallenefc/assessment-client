<template>
  <div class="app-container">
    <div class="column" v-for="column in columns" :key="column">
      <Column v-bind:column="column" v-on:delete-column="handleDeleteColumn" v-on:edit-column="handleEditColumnTitle" v-on:add-card="handleAddCard"/>
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
    handleEditColumnTitle(id, title) {
      this.columns = this.columns.map(column => {
        if (column.id === id) return ({
          ...column,
          title,
        })
        else return column;
      });
    },
    handleAddCard (id, card) {
      this.columns = this.columns.map(column => {
        if (column.id === id) {
          return ({
            ...column,
            cards: [...column.cards, card]
          })
        }
        else return column;
      });
    },
    async handleAddColumn() {
      try {
        const response = await axios.post('http://localhost:8000/api/columns', {
          title: 'New Column',
        });
        this.columns = [...this.columns, {
          ...response.data,
          cards: [],
        }];
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

<style lang="scss">
  @import 'main.scss'
</style>
