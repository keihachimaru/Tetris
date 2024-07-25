<template>
    <div class="figure">
        <CubeDisplay 
            :x="copy.cubePos[0].x + 3" 
            :y="copy.cubePos[0].y + 3" 
            :class="copy.type"/>
        <CubeDisplay 
            :x="copy.cubePos[1].x + 3" 
            :y="copy.cubePos[1].y + 3" 
            :class="copy.type"/>
        <CubeDisplay 
            :x="copy.cubePos[2].x + 3" 
            :y="copy.cubePos[2].y + 3" 
            :class="copy.type"/>
        <CubeDisplay 
            :x="copy.cubePos[3].x + 3" 
            :y="copy.cubePos[3].y + 3" 
            :class="copy.type"/>
    </div>
  </template>
  
  <script>
  import CubeDisplay from '@/components/CubeDisplay.vue'

  export default {
    name: 'TetrisDisplayFigure',
    components: {
        CubeDisplay
    },
    props: {
      fig: Object,
    },
    data() {
        return {
            copy: {...this.fig},
        }
    },
    methods: {
        rotate(cubePos) {
            return cubePos.map(pos => {
                return {x: pos.y, y: -pos.x};
            });
        },
        updateCopy() {
          this.copy = JSON.parse(JSON.stringify(this.fig));
          if (this.copy.type == "I") {
              this.copy.cubePos = this.rotate(this.copy.cubePos);
          }
          if (this.copy.type == "T") {
              this.copy.cubePos = this.rotate(this.copy.cubePos);
              this.copy.cubePos = this.rotate(this.copy.cubePos);
              this.copy.cubePos = this.rotate(this.copy.cubePos);
              this.copy.x += 1;
          }
          if (this.copy.type == "J") {
              this.copy.cubePos.forEach((pos)=>{
                  pos.x += 1;
              });
          }
          if (this.copy.type == "O") {
              this.copy.cubePos.forEach((pos)=>{
                  pos.y += 1;
              });
          }
      }
    },
    watch: {
        fig: {
        handler: 'updateCopy',
        immediate: true,
        deep: true
        }
    },
    mounted() {
        this.updateCopy();
    },
    
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
  
  