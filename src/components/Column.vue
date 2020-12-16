<template>
  <div>
    <div class="column-content">
      <button v-on:click="handleOpenModal">Edit</button>
      <transition name="fade" appear>
        <div class="modal-overlay" v-if="showModal">
          <input type="text" v-model="editInput"/>
          <button v-on:click="editColumn">Change Title</button>
          <button v-on:click="handleCloseModal">Close</button>
        </div>
      </transition>
    {{column.title}}
    <button type="button" v-on:click="deleteColumn">X</button>
   </div>
    <div class="cards" v-for="card in column.cards" :key="card">
      <Card v-bind:card="card" v-on:edit-card="editCard"/>
    </div>
    <button v-on:click="addCard">Add Card</button>
  </div>
</template>

<script>

import axios from 'axios';
import Card from './Card';

export default {
  name: 'Column',
  props: {
    column: Object,
    handleDeleteColumn: Function,
  },
  data: function() {
    return {
      showModal: false,
      editInput: '',
    }
  },
    components: {
    Card,
  },
  mounted() {
    console.log(this.column);
  },
  methods: {
    async deleteColumn () {
      try {
        await axios.delete(`http://localhost:8000/api/columns/${this.column.id}`);
        this.$emit('delete-column', this.column.id);
      } catch (err) {
        console.error(err);
      }
    },
    async editColumn () {
      try {
        await axios.put(`http://localhost:8000/api/columns/${this.column.id}`, {
          title: this.editInput,
        })
        this.$emit('edit-column', this.column.id, this.editInput);
      } catch (err) {
        console.error(err);
      }
    },
    async addCard () {
      try {
        const response = await axios.post(`http://localhost:8000/api/cards/${this.column.id}`, {
          title: 'New Card',
          description: 'New Card Description',
        });
        this.$emit('add-card', this.column.id, response.data);
      } catch (err) {
        console.error(err);
      }
    },
    editCard (id, title, description) {
      console.log({id, title, description})
      this.column.cards = this.column.cards.map(card => {
        console.log(card)
        if (card.id === id) {
          return {
            ...card,
            title,
            description
          }
        }
        else return card;
      })
    },
    handleCloseModal () {
      this.showModal = false;
    },
    handleOpenModal () {
      this.showModal = true;
    },
  }
}
</script>

<style scoped>

.modal-overlay {
  width: 500px;
  height: 500px;
  position: absolute;
  background-color: red;
  z-index: 5;
}

.cards {
  border: 2px solid blue;
}

.column-content {
  min-width: 200px;
}

</style>
