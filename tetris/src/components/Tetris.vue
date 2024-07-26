<template>
  <div class="container" @keydown="controls">
    <div class="col">
      <div class="hold-outline">
        <div class="hold">
            <FigureDisplay v-if="hold" :fig="hold"/>
        </div>
      </div>
      <div class="stats-outline">
        <div class="stats">
          <p><span>Lines:</span><span>{{ lines }}</span></p>
          <p><span>Score:</span><span>{{ score }}</span></p>
        </div>
      </div>
    </div>
    <div class="tetris-outline">
      <div class="tetris">
        <Figure v-for="(figure, i) in figures" :fig="figure" :key="i"/>
      </div>
    </div>
    <div class="col">
      <div class="next-outline">
        <div class="next">
          <FigureDisplay v-if="next" :fig="next"/>
        </div>
      </div>
    </div>
    <div class="pause" v-if="!run">
      <div @click="run=true" id="pause"></div>
    </div>
  </div>
</template>

<script>
import Figure from '@/components/Figure.vue'
import FigureDisplay from '@/components/FigureDisplay.vue'

export default {
  name: 'TetrisContainer',
  props: {
    msg: String
  },
  data() { 
    return {
      figures: [],
      score: 0,
      lines: 0,
      difficulty: 1,
      floor: [20,20,20,20,20,20,20,20,20,20],
      current: null,
      run: false,
      hold: false,
      next: null,
      canSwap: true,
    }
  },
  components: {
    Figure,
    FigureDisplay
  },
  methods: {
    eraseLine(line) {
      this.figures.forEach((figure) => {
        let affected = figure.cubePos.filter((pos)=>(pos.y+figure.y) <= line).length > 0
        figure.cubePos = figure.cubePos.filter((pos)=>(pos.y+figure.y) != line)

        if (affected) {
          figure.y += 1
        }
      })
      this.lines += 1
      this.score += 10*this.difficulty

      this.difficulty = parseInt(this.score/10)
    },
    checkLines() {
      let lines = {}
      this.figures.forEach((figure) => {
        for (let i=0; i<figure.cubePos.length; i++) {
          let line = figure.cubePos[i].y+figure.y
          if (line in lines) {
            lines[line] += 1
          }
          else {
            lines[line] = 1
          }
        }
      })
      let completed = [];

      Object.keys(lines).forEach(key => {
        if (lines[key] >= 10) {
          completed.push(parseInt(key));
        }
      });
      
      for (let i=0; i<completed.length; i++) {
        this.eraseLine(completed[i])
      }
    },
    rotate(cubePos) {
      return cubePos.map(pos => {
        return {x: -pos.y, y: pos.x};
      });
    },
    canRotate() {
      let positions = []
      let possible = true

      let target = this.figures.find((figure) => figure.id == this.current)

      if (target.type == 'O') {
        return false;
      } 
      
      this.figures.forEach((figure) => {
        if (figure.id != this.current) {
          for (let i=0; i<figure.cubePos.length; i++) {
            positions.push([
              figure.cubePos[i].x+figure.x,
              figure.cubePos[i].y+figure.y,
            ].join(','))
          }
        }
      })

      let newCubePos = this.rotate(target.cubePos)

      for (let i=0; i<newCubePos.length; i++) {
        possible = possible && !positions.includes([
        newCubePos[i].x+target.x,
        newCubePos[i].y+target.y,
        ].join(','))
        possible = possible && (newCubePos[i].x+target.x > 0)
        possible = possible && (newCubePos[i].x+target.x < 11)
        possible = possible && (newCubePos[i].y+target.y < 21)
      }
      
      return possible
    },
    noCollisions(fig) {
      let positions = []
      let possible = true

      let target = fig
      this.figures.forEach((figure) => {
        if ((figure.id != fig.id) && (figure.id != this.current)) {
          for (let i=0; i<figure.cubePos.length; i++) {
            positions.push([
              figure.cubePos[i].x+figure.x,
              figure.cubePos[i].y+figure.y,
            ].join(','))
          }
        }
      })
      for (let i=0; i<target.cubePos.length; i++) {
        possible = possible && !positions.includes([
          target.cubePos[i].x + target.x,
          target.cubePos[i].y + target.y,
        ].join(',')) 
        possible = possible && ((target.cubePos[i].x + target.x) > 0)
        possible = possible && ((target.cubePos[i].x + target.x) < 11)
        possible = possible && ((target.cubePos[i].y + target.y) < 21)
      }

      return possible
    },
    canGoDir(dir) {
      let positions = []
      let possible = true

      let target = this.figures.find((figure) => figure.id == this.current)

      this.figures.forEach((figure) => {
        if (figure.id != this.current) {
          for (let i=0; i<figure.cubePos.length; i++) {
            positions.push([
              figure.cubePos[i].x+figure.x,
              figure.cubePos[i].y+figure.y,
            ].join(','))
          }
        }
      })

      let newX = target.x - dir[0]
      let newY = target.y - dir[1]

      for (let i=0; i<target.cubePos.length; i++) {
        possible = possible && !positions.includes([
          target.cubePos[i].x+newX,
          target.cubePos[i].y+newY,
        ].join(',')) 
        possible = possible && (target.cubePos[i].x+newX > 0)
        possible = possible && (target.cubePos[i].x+newX < 11)
        possible = possible && (target.cubePos[i].y+newY < 21)
      }
      
      return possible
    },
    controls(e) {
      if (this.run) {
        if (e.key == 'ArrowLeft') {
          this.figures.forEach((figure) => {
            if (figure.id == this.current && this.canGoDir([1, 0])) {
              figure.x -= 1;
            }
          })
        }
        if (e.key == 'ArrowRight') {
          this.figures.forEach((figure) => {
            if (figure.id == this.current && this.canGoDir([-1, 0])) {
              figure.x += 1;
            }
          })
        }
        if (e.key == 'ArrowUp') {
          this.figures.forEach((figure) => {
            if (figure.id == this.current && this.canRotate()) {
              figure.cubePos = this.rotate(figure.cubePos)
            }
          })
        }
        if (e.key == 'ArrowDown') {
          this.figures.forEach((figure) => {
            if (figure.id == this.current && this.canGoDir([0, -1])) {
              figure.y += 1;
            }
          })
        }
        if (e.key == 'c' && this.canSwap) {
          if (this.hold) {
            let currentFig = this.figures.find((fig) => fig.id == this.current)
            let currentX = currentFig.x
            let currentY = currentFig.y
            let newId = this.hold.id

            let copy = {...this.hold}
            copy.x = currentX
            copy.y = currentY

            if (this.noCollisions(copy)) {
              this.hold.x = currentX
              this.hold.y = currentY
              this.figures.push(this.hold)

              this.hold = currentFig
              this.figures = this.figures.filter((fig) => fig.id != this.current)

              this.current = newId
            }
          }
          else {
            let previousFig = this.figures.find((fig) => fig.id == this.current)
            let previousX = previousFig.x
            let previousY = previousFig.y

            this.hold = this.figures.find((fig) => fig.id == this.current)
            this.figures = this.figures.filter((fig) => fig.id != this.current)

            this.newFigure()
            let newCurrent = this.figures.find((fig) => fig.id == this.current)
            newCurrent.x = previousX
            newCurrent.y = previousY
          }
          this.canSwap = false
        }
        if (e.key == ' ') {
          let current = this.figures.find(fig => fig.id == this.current)
          while (this.canGoDir([0, -1])) {
            current.y += 1
          }
          this.canSwap = true
          this.checkLines();
          this.updateFloor(current);
          this.newFigure();
        }
      }
      if (e.key == 'Escape') {
        this.run = !this.run
      }
    },
    resetGame() {
      this.figures = []
      this.score = 0
      this.lines = 0
      this.difficulty = 0
      this.floor = [20,20,20,20,20,20,20,20,20,20]
      this.current = null
      this.run = false
      this.hold = null
      this.next = null
      this.canSwap = true
    },
    createFigure() {
      let type = ['O', 'L', 'I', 'T', 'S', 'Z', 'J'][Math.floor(Math.random() * (6 - 0 + 1)) + 0]
      let x = 5;
      let y = -1;
      let id = Math.floor(Math.random() * (1000000))
      let cubePos;

      if (type == 'O') {
        cubePos = [
          {x: 0, y: -1},
          {x: 1, y: -1},
          {x: 0, y: 0},
          {x: 1, y: 0}
        ]
      }
      if (type == 'T') {
        cubePos = [
          {x: -1, y: 0},
          {x: 0, y: -1},
          {x: 1, y: 0},
          {x: 0, y: 0}
        ]
      }
      if (type == 'L') {
        cubePos = [
          {x: 0, y: -1},
          {x: 0, y: 0},
          {x: 0, y: 1},
          {x: 1, y: 1}
        ]
      }
      if (type == 'J') {
        cubePos = [
          {x: 0, y: -1},
          {x: 0, y: 0},
          {x: 0, y: 1},
          {x: -1, y: 1}
        ]
      }
      if (type == 'I') {
        cubePos = [
          {x: 0, y: -1},
          {x: 0, y: 0},
          {x: 0, y: 1},
          {x: 0, y: 2}
        ]
      }
      if (type == 'S') {
        cubePos = [
          {x: 1, y: -1},
          {x: 0, y: 0},
          {x: 1, y: 0},
          {x: 0, y: 1}
        ]
      }
      if (type == 'Z') {
        cubePos = [
          {x: 1, y: 1},
          {x: 0, y: 0},
          {x: 1, y: 0},
          {x: 0, y: -1}
        ]
      }

      let figure = {
        x: x,
        y: y,
        cubePos: cubePos,
        type: type,
        id: id,
      }
      
      return figure
    },
    updateFloor(figure) {
      let updates = []
      for (let i=0; i<figure.cubePos.length; i++) {
        let newFloor = figure.y + figure.cubePos[i].y - 1
        let xIndex = figure.x + figure.cubePos[i].x - 1

        if (this.floor[xIndex]>newFloor) {
          this.floor[xIndex] = newFloor
        }
        if (!updates.includes(xIndex)){
          updates.push(xIndex)
        }
      }
      
      for (let i=0; i<this.floor.length; i++) {
        if (this.floor[i]<=0) {
          this.resetGame()
        }
      }
    },
    updateCurrent() {
      setInterval(()=>{
        if (this.run) {
          this.figures.forEach((figure) => {
            if (figure.id == this.current) {
              if (this.canGoDir([0, -1])) {
                figure.y += 1;
              }
              else {
                this.canSwap = true
                this.checkLines();
                this.updateFloor(figure);
                this.newFigure();
              }
            }
          })
        }
      }, 1000-100*this.difficulty)
    },
    newFigure() {
      if (!this.next) {
        this.next = this.createFigure()
        let newFig = this.createFigure()
        this.figures.push(newFig)
        this.current = newFig.id
      }
      else {
        this.current = this.next.id
        this.figures.push(this.next)
        this.next = this.createFigure()
      }
    }
  },
  mounted() {
    this.updateCurrent()
    this.newFigure()

    window.addEventListener('keydown', this.controls)
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#pause {
   width: 0px;
   height: 0px;
   border-style: solid;
   border-width: 0 17.5px 30.3px 17.5px;
   border-color: transparent transparent #FFFFFF transparent;
   transform: rotate(90deg);
   transition: all .3s ease-in-out;
}
#pause:hover {
  filter: brightness(.7);
  cursor: pointer;
}
.pause {
  height: 100%;
  width: 100%;
  background: rgba(0,0,0,.3);
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
}
.container:has(.pause) .tetris-outline , .container:has(.pause) .col {
  filter: blur(3px);
}
.container {
  height: calc(100% - 100px);
  width: 100%;
  display: flex;
  justify-content: center;
  position: relative;
  align-items: center;
  justify-content: center;
  gap: 30px;
}

.col {
  height: 80%;
  width: fit-content;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.next-outline {
  height: 25%;
  aspect-ratio: 6/5;
  border: 5px solid #444;
  border-radius: 12px;
  background-color: #444;
}
.next {
  height: 100%;
  aspect-ratio: 6/5;
  border-right: 2px solid #333;
  border-bottom: 2px solid #333;
  background-image: linear-gradient(#181818 2px, transparent 1px), linear-gradient(90deg, #181818 2px, #111 1px);
  background-size: 16.67% 20%;
  border-radius: 8px;
  position: relative;
}

.hold-outline {
  height: 25%;
  aspect-ratio: 6/5;
  border: 5px solid #444;
  border-radius: 12px;
  background-color: #444;
}
.hold {
  height: 100%;
  aspect-ratio: 6/5;
  border-right: 2px solid #333;
  border-bottom: 2px solid #333;
  background-image: linear-gradient(#181818 2px, transparent 1px), linear-gradient(90deg, #181818 2px, #111 1px);
  background-size: 16.67% 20%;
  border-radius: 8px;
  position: relative;
}

.stats-outline {
  height: 25%;
  aspect-ratio: 6/5;
  border: 5px solid #444;
  border-radius: 12px;
  background-color: #444;
}
.stats {
  height: 100%;
  aspect-ratio: 6/5;
  border-right: 2px solid #333;
  border-bottom: 2px solid #333;
  background: #111;
  background-size: 16.67% 20%;
  border-radius: 8px;
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: white;
  gap: 20px;
}
.stats p {
  width: 60%;
  margin: 0px;
  text-align: start;
  font-weight: bold;
  display: flex;
  justify-content: space-between;
}

.tetris-outline {
  height: 80%;
  width: fit-content;
  border-radius: 15px;
  background-color: #444;
  border: 5px solid #444;
  overflow: hidden;
  box-sizing: border-box;
}
.tetris {
  height: 100%;
  aspect-ratio: 10/20;
  border-right: 2px solid #181818;
  border-bottom: 2px solid #181818;
  background-image: linear-gradient(#181818 2px, transparent 1px), linear-gradient(90deg, #181818 2px, #111 1px);
  background-size: 10% 5%;
  position: relative;
  overflow: hidden;
  border-radius: 8px;
}
</style>
