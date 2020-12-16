<template>
  <div class="card" v-on:click="handleOpenModal" :id="card.id">
      <transition name="fade" appear>
        <div class="modal-overlay" v-if="showModal">
          Title
          <input type="text" v-model="titleInput" placeholder="title"/>
          <br>
          Description
          <input type="text" v-model="descInput" placeholder="description"/>
          <br>
          <button v-on:click="editCard">Change Card</button>
          <button v-on:click="handleCloseModal">Close</button>
        </div>
      </transition>
    {{card.title}}
  </div>
</template>

<script>

import axios from 'axios';

export default {
  name: 'Card',
  props: {
    card: Object,
  },
  data: function() {
    return {
      showModal: false,
      titleInput: this.card.title,
      descInput: this.card.description,
    }
  },
  mounted() {
  },
  methods: {
    async editCard () {
      try {
        axios.put(`http://localhost:8000/api/cards/${this.card.id}`, {
          title: this.titleInput,
          description: this.descInput,
        });
        this.$emit('edit-card', this.card.id, this.titleInput, this.descInput);
      } catch (err) {
        console.error(err);
      }
    },
    handleOpenModal() {
      this.showModal = true;
    },
    handleCloseModal() {
      this.$emit('edit-card', this.card.id, this.titleInput, this.descInput);
      this.showModal = false;
      console.log(this.showModal);
    }
  }
}
</script>

<style scoped>

.modal-overlay {
  top: 40%;
  left: 40%;
  width: 500px;
  height: 500px;
  position: absolute;
  background-color: rgb(255, 0, 0);
  z-index: 5;
}

</style>
