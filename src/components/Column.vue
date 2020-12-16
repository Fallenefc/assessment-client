<template>
<div class="column-container">
      <div class="title">
      <button v-on:click="handleOpenModal" class="title__edit">Edit</button>
      <transition name="fade" appear>
        <div class="modal-overlay" v-if="showModal">
          {{column.title}}
          <input type="text" v-model="editInput" class="modal-overlay__input"/>
          <button v-on:click="editColumn" class="modal-overlay__btn-change">Change Title</button>
          <button v-on:click="handleCloseModal" class="modal-overlay__btn-close">Close</button>
        </div>
      </transition>
      <span class="title__column-title">{{column.title}}</span>
      <button type="button" class="title__close" v-on:click="deleteColumn">X</button>
    </div>
      <div class="column-component">
    <draggable v-model="column.cards" group="cards" @start="drag=true" @end="handleRelationUpdate">
      <div class="cards" v-for="card in column.cards" :key="card" :id="id"> 
        <Card v-bind:card="card" v-on:edit-card="editCard"/>
      </div>
    </draggable>
  </div>
    <button v-on:click="addCard" class="column-component__add-card-btn">Add Card</button>
  </div>
</template>

<script>

import axios from 'axios';
import Card from './Card';
import draggable from 'vuedraggable';

export default {
  name: 'Column',
  props: {
    column: Object,
    handleDeleteColumn: Function,
  },
  data: function() {
    return {
      showModal: false,
      editInput: this.column.title,
      debounce: false,
      id: this.column.id,
    }
  },
    components: {
    Card,
    draggable,
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
    async handleRelationUpdate (event) {
      try {
        const columnId = event.to.firstChild.id;
        const id = event.clone.firstChild.id;
        console.log(event);
        await axios.put(`http://localhost:8000/api/cards/${id}`, {
          column_id: +columnId,
        });
      } catch(err) {
        console.error(err);
      }
    }
  }
}
</script>

<style lang="scss">
  @import 'column.scss'
</style>
