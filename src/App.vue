<template>
  <div class="app-container">
    <div class="column" v-for="column in columns" :key="column">
      <Column v-bind:column="column" v-on:delete-column="handleDeleteColumn" v-on:edit-column="handleEditColumnTitle" v-on:add-card="handleAddCard"/>
    </div>
    <div class="add-and-export">
        <button v-on:click="handleAddColumn" class="app-container__add-column-btn">Add Column</button>
        <button v-on:click="handleExportDb" class="app-container__export-btn">Export DB</button>
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
      columns: []
    }
  },
  methods: {
    handleDeleteColumn(id) {
      this.columns = this.columns.filter(column => {
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
        const response = await axios.post(`${process.env.VUE_APP_SERVER_URL}/api/columns`, {
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
    async handleExportDb() {
      try {
        await axios.get(`${process.env.VUE_APP_SERVER_URL}/api/dbdump`);
        const url = `${process.env.VUE_APP_SERVER_URL}/dump.sql`;
        const link = document.createElement('a');
        link.href = url;
        link.setAttribute('download', 'download.sql');
        document.body.appendChild(link);
        link.click();
      } catch (err) {
        console.error(err);
      }
    },
  },
  mounted() {
    try {
      axios.get(`${process.env.VUE_APP_SERVER_URL}/api/cards`).then(res => {
      this.columns = res.data;
    });
    }
    catch (err) {
      console.error(err);
    }
  }
}
</script>

<style lang="scss">
  @import 'main.scss'
</style>
