<template>
  <div class="hello">
    {{card.title}}
  </div>
</template>

<script>

// import axios from 'axios';

export default {
  name: 'Card',
  props: {
    card: Object,
  },
  data: function() {
    return {
      showModal: false,
    }
  },
  mounted() {
    console.log(this.handleDeleteColumn);
  },
  methods: {
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

</style>
