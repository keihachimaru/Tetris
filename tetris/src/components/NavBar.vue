<template>
    <div class="navbar">
      <div style="width: 50px"></div>
      <div class="title">
        TETRIS
      </div>
      <a href="https://github.com/keihachimaru">
        <img src="@/assets/github.webp" height="50px" width="50px"/>
      </a>
    </div>
  </template>
  
  <script>
  export default {
    name: 'NavBar',
    components: {

    },
    props: {
      fig: Object,
    },
    data() {
        return {
        }
    },
    methods: {
    },
    mounted() {
    }
  }
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  .navbar {
    width: 100%;
    height: 100px;
    background: #333;
    display: flex;
    justify-content: space-between;
    box-sizing: border-box;
    padding: 10px;
    align-items: center;
    padding-left: 20px;
    padding-right: 20px;
  }
  .title {
    color: white;
    font-weight: bold;
    font-size: 30px;
    user-select: none;
  }
  </style>
  
  