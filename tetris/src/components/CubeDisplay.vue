<template>
    <div class="cube" :style="{ 'top': (y-1)*20 + '%', 'left': (x-1)*16.67 + '%' }">
    </div>
  </template>
  
  <script>
  export default {
    name: 'CubePiece',
    props: {
      x: Number,
      y: Number,
      type: String,
    }
  }
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  .cube {
    height: 20%;
    width: 16.67%;
    position: absolute;
    transform: translate(1px, 1px);
    border: 1px solid #333;
    box-sizing: border-box;
    border-radius: 3px;
  }
  .L {
    background-color: rgb(236, 179, 56);
  }
  .J {
    background-color: rgb(56, 149, 236);
  }
  .T {
    background-color: rgb(134, 56, 236);
  }
  .O {
    background-color: rgb(56, 236, 110);
  }
  .I {
    background-color: rgb(56, 56, 236);
  }
  .S {
    background-color: rgb(56, 236, 206);
  }
  .Z {
    background-color: rgb(236, 56, 146);
  }
  </style>
  