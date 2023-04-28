<template>
    <div class="modal">
      <div class="modal-content">
        <h2>{{ title }}</h2>
        <p>{{ message }}</p>
        <button class="close" @click="$emit('close')">Close</button>
      </div>
      <div class="modal-overlay"></div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      title: String,
      message: String
    }
  }
  </script>
  
  <style scoped>
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .modal-content {
    background-color: #fff;
    padding: 20px;
    box-shadow: 0 0 10px rgba(0,0,0,0.5);
    z-index: 1;
    max-width: 400px;
    text-align: center;
  }
  
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background-color: rgba(0,0,0,0.5);
    z-index: 0;
  }
  </style>
  