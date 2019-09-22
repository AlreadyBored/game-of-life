<template>
  <div>
    <div v-if="!dimensionRecieved">
      <h1>Enter playfield dimensions </h1>
      <label>
        Rows
        <input type="text" v-model.number="dimensions.rows">
      </label>
      <br>
      <label>
        Columns
        <input type="text" v-model.number="dimensions.cols">
      </label>
      <br>
      <button @click='getDimensions'>Submit</button>
    </div>
    <div v-if="dimensionRecieved">
      <h1>Playfield</h1>
      <label>iterationsCount
        <input type="text" v-model.number="iterationsNeeded">
      </label>
      <hr>
      <label> Frequency(ms)
        <input type="text" v-model.number="frequency">
      </label>          
      <button @click='slideshow'>Calculate next gen</button>
      <div  v-if='!tmblr'
            class="playfield">
        <div v-for="(row, indexR) in playfield" 
             :key="'r' + indexR"
             class='playfield-row'>
          <div v-for="(cell, indexС) in row" 
               @click='setType(playfield, indexR, indexС)'
               :key="'c' + indexС"
               class='playfield-cell'
               :class='statusCell(playfield, indexR, indexС)'></div>
       </div>
      </div>
      <div  v-if='tmblr'
            class="playfield">
        <div v-for="(row, indexR) in nextTurn" 
             :key="'r' + indexR"
             class='playfield-row'>
          <div v-for="(cell, indexС) in row" 
               @click='setType(nextTurn, indexR, indexС)'
               :key="'c' + indexС"
               class='playfield-cell'
               :class='statusCell(nextTurn, indexR, indexС)'></div>
       </div>
      </div>      
    </div>
  </div>
</template>

<script>
export default {
  name: 'Playfield',
  data() {
    return {
      dims: '',
      tmblr: false,
      dimensions: {
        rows: null,
        cols: null
      },
      dimensionRecieved: false,
      playfield: [],
      nextTurn: [],
      iterationsNeeded: '',
      iterationsCount: '',
      frequency: ''
    }
  },
  methods: {
    getDimensions() {
      this.dimensionRecieved = true;
      this.fillArrays();
    },
    fillArrays() {
      for(let i = 0; i < this.dimensions.rows; i++) {
        const row = [];
        for(let i = 0; i < this.dimensions.cols; i++) {
          row.push(false);
        }
        this.playfield.push(row);
        this.nextTurn.push(Object.assign([],row));
      }
    },
    slideshow() {
      const self = this;
      let timer = setTimeout(function tick() {
        if (self.iterationsCount === self.iterationsNeeded){
          self.iterationsCount = 0;
          return;
        }  
        self.nextGeneration();
        self.switchGenerations();        
        self.iterationsCount++;
        timer = setTimeout(tick, self.frequency);
      }, self.frequency);
    },
    nextGeneration() {
      const fPrev = this.tmblr ? this.playfield : this.nextTurn;
      const fCur = this.tmblr ? this.nextTurn : this.playfield;

      for(let i = 0; i < this.dimensions.rows; i++) {
        for(let j = 0; j < this.dimensions.cols; j++) {
          let cnt = 0;
          let resArr;
          if(i === 0) {
            if(j === 0) {
              resArr = [
                   fCur[i+1][j],
                   fCur[i+1][j+1],
                   fCur[i][j+1]
                  ];
            } else if(j === this.dimensions.cols - 1) {
            resArr = [
              fCur[i][j-1],
              fCur[i+1][j-1],
              fCur[i+1][j]
            ];
            } else {
              resArr = [
                fCur[i][j-1],
                fCur[i+1][j-1],
                fCur[i+1][j],
                fCur[i+1][j+1],
                fCur[i][j+1]
              ];
            }
          } else if(i === this.dimensions.rows - 1) {
            if(j === 0) {
              resArr = [
                fCur[i-1][j],
                fCur[i-1][j+1],
                fCur[i][j+1]
              ]
            } else if(j === this.dimensions.cols - 1) {
              resArr = [
                fCur[i][j-1],
                fCur[i-1][j-1],
                fCur[i-1][j]
              ];
            } else {
              resArr = [
                fCur[i][j-1],
                fCur[i-1][j-1],
                fCur[i-1][j],
                fCur[i-1][j+1],
                fCur[i][j+1]
              ];
            }
          } else {
            if(j === 0) {
              resArr = [
                fCur[i-1][j],
                fCur[i-1][j+1],
                fCur[i][j+1],
                fCur[i+1][j+1],
                fCur[i+1][j]
              ];
            } else if(j === this.dimensions.cols - 1) {
              resArr = [
                fCur[i-1][j],
                fCur[i-1][j-1],                
                fCur[i][j-1],
                fCur[i+1][j-1],
                fCur[i+1][j]
              ];
            } else {
              resArr = [
                fCur[i-1][j-1],
                fCur[i][j-1],
                fCur[i+1][j-1],
                fCur[i+1][j],
                fCur[i+1][j+1],
                fCur[i][j+1],
                fCur[i-1][j+1],
                fCur[i-1][j]
              ];
            }
          }

            resArr.forEach(x => {
              if(x === true) cnt++;
            });
              switch(cnt) {
              case 0:              
              case 1:              
              this.$set(fPrev[i], j, false);
              break;
              case 2:
              this.$set(fPrev[i], j, fCur[i][j]);
              break;
              case 3:
              this.$set(fPrev[i], j, true);
              break;
              case 4:
              case 5:
              case 6:
              case 7:
              case 8:
              this.$set(fPrev[i], j, false);
              break;
              default:
              throw new Error('Error of cnt calc!');
              }
        }
      }
    },
    switchGenerations() {
      this.tmblr = !this.tmblr;
    },
    setType(field, i, j) {
      this.$set(field[i], j, !field[i][j]);
    }
  },
  computed: {
    statusCell(field, row, cell) {
      return (field, row, cell) => {
        switch (field[row][cell]) {
          case false:
            return "cell-dead";
            break;

          case true:
            return "cell-alive";
            break;

          default:
            throw new Error("Wrong cell adress and/or value");
        }
      }
    }
  }
}
</script>


<style scoped>
.playfield {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.playfield-row {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.playfield-cell {
  border: 1px solid green;
  width: 25px;
  height: 25px;
  margin: 1px;
}

.cell-alive {
  background-color: green;
}

.cell-dead {
  background-color: yellow;
}
</style>

    },
    nextGeneration() {
      const f = this.playfield,
      fn = this.nextTurn;

      for(let i = 0; i < this.dimensions.rows; i++) {
        console.log(1);
        for(let j = 0; j < this.dimensions.cols; j++) {
          let cnt = 0;
          let resArr;
          console.log(2);
          if(i === 0) {
            if(j === 0) {
              resArr = [
                   f[i+1][j],
                   f[i+1][j+1],
                   f[i][j+1]
                  ];
            } else if(j === this.dimensions.cols - 1) {
            resArr = [
              f[i][j-1],
              f[i+1][j-1],
              f[i+1][j]
            ];
            } else {
              resArr = [
                f[i][j-1],
                f[i+1][j-1],
                f[i+1][j],
                f[i+1][j+1],
                f[i][j+1]
              ];
            }
          } else if(i === this.dimensions.rows - 1) {
            if(j = 0) {
              resArr = [
                f[i-1][j],
                f[i-1][j+1],
                f[i][j+1]
              ]
            } else if(j === this.dimensions.cols - 1) {
              resArr = [
                f[i][j-1],
                f[i-1][j-1],
                f[i-1][j]
              ];
            } else {
              resArr = [
                f[i][j-1],
                f[i-1][j-1],
                f[i-1][j],
                f[i-1][j+1],
                f[i][j+1]
              ];
            }
          } else {
            if(j === 0) {
              resArr = [
                f[i-1][j],
                f[i-1][j+1],
                f[i][j+1],
                f[i+1][j+1],
                f[i+1][j]
              ];
            } else if(j === this.dimensions.cols - 1) {
              resArr = [
                f[i-1][j],
                f[i-1][j-1],                
                f[i][j-1],
                f[i+1][j-1],
                f[i+1][j]
              ];
            } else {
              resArr = [
                f[i-1][j-1],
                f[i][j-1],
                f[i+1][j-1],
                f[i+1][j],
                f[i+1][j+1],
                f[i][j+1],
                f[i-1][j+1],
                f[i-1][j]
              ];
            }
          }

            resArr.forEach(x => {
              if(x === true) cnt++;
            });
            if(this.tmblr === false) {
              switch(cnt) {
              case (cnt < 2):
              fn[i][j] = false;
              break;
              case (cnt === 2):
              fn[i][j] = f[i][j];
              break;
              case (cnt === 3):
              fn[i][j] = true;
              break;
              case (cnt > 3):
              fn[i][j] = false;
              break;
              }
            } else {
              switch(cnt) {
              case (cnt < 2):
              f[i][j] = false;
              break;
              case (cnt === 2):
              f[i][j] = fn[i][j];
              break;
              case (cnt === 3):
              f[i][j] = true;
              break;
              case (cnt > 3):
              f[i][j] = false;
              break;
              }
            }

        }
      }
    },
    switchGenerations() {
      this.tmblr = !this.tmblr;
    },
    setType(field, i, j) {
      this.$set(field[i], j, !field[i][j]);
    }
  },
  computed: {
    statusCell(field, row, cell) {
      return (field, row, cell) => {
        switch (field[row][cell]) {
          case false:
            return "cell-dead";
            break;

          case true:
            return "cell-alive";
            break;

          default:
            throw new Error("Wrong cell adress and/or value");
        }
      }
    }
  }
}
</script>


<style scoped>
.playfield {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.playfield-row {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.playfield-cell {
  border: 1px solid green;
  width: 25px;
  height: 25px;
  margin: 10px;
}

.cell-alive {
  background-color: green;
}

.cell-dead {
  background-color: yellow;
}
</style>
