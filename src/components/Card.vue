<template>
  <div class="card" v-on:click="handleOpenModal" :id="card.id">
      <transition name="fade" appear>
        <div class="modal-overlay" v-if="showModal">
          Title
          <input type="text" v-model="titleInput" placeholder="title" class="modal-overlay__input"/>
          <br>
          Description
          <textarea v-model="descInput" placeholder="description" class="modal-overlay__textarea"/>
          <br>
          <button v-on:click="editCard" class="modal-overlay__btn-change">Change Card</button>
          <button v-on:click="handleCloseModal" class="modal-overlay__btn-close">Close</button>
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

<style scoped lang="scss">
  @import 'card.scss'
</style>