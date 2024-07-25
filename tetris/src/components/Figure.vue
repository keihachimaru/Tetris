<template>
    <div class="figure">
        <Cube
            v-for="(pos, i) in fig.cubePos"
            :x="pos.x + fig.x" 
            :y="pos.y + fig.y" 
            :class="fig.type" :key="i"/>
    </div>
  </template>
  
  <script>
  import Cube from '@/components/Cube.vue'

  export default {
    name: 'TetrisFigure',
    components: {
        Cube
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
  .figure {
    height: 100%;
    width: 100%;
    position: absolute;
  }
  </style>
  
  