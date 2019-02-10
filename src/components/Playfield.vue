<template>
  <div>
    <div v-if="!dimensionsArrived">
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
    <div v-if="dimensionsArrived">
      <h1>Playfield</h1>
      <button @click='switchGenerations'>Switch to nex gen</button>
      <button @click='nextGeneration'>Calculate next gen</button>
      <div class="playfield">
        <div v-for="(row, indexR) in playfield" 
             :key="'r' + indexR"
             class='playfield-row'>
          <div v-for="(cell, indexС) in row" 
               @click='setType(indexR, indexС)'
               :key="'c' + indexС"
               class='playfield-cell'
               :class='statusCell(indexR, indexС)'></div>
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
      dimensions: {
        rows: null,
        cols: null
      },
      dimensionsArrived: false,
      playfield: [],
      nextTurn: []
    }
  },
  methods: {
    getDimensions() {
      this.dimensionsArrived = true;
      this.fillArrays();
    },
    fillArrays() {
      for(let i = 0; i < this.dimensions.rows; i++) {
        const row = [];
        for(let i = 0; i < this.dimensions.cols; i++) {
          row.push(false);
        }
        this.playfield.push(row);
        this.nextTurn.push(row);
      }
    },
    nextGeneration() {
      const f = this.playfield,
      fn = this.nextTurn;
      for(let i = 0; i < f.length; i++) {
        for(let j = 0; j < f[i].length; j++) {
          let cnt = 0;
          const cellType = f[i][j];
          console.log(f[i-1])
          const resArr = [
            f[i-1][j-1],
            f[i][j-1],
            f[i+1][j-1],
            f[i+1][j],
            f[i+1][j+1],
            f[i][j+1],
            f[i-1][j+1],
            f[i-1][j]
            ];
            console.log(resArr);
            resArr.forEach(x => {
              if(x === true) cnt++;
            });
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
              default:
              throw new Error('Error in defining cell type');
            }
        }
      }
    },
    switchGenerations() {
      this.playfield = this.nextTurn;
    },
    setType(i, j) {
      this.$set(this.playfield[i], j, !this.playfield[i][j]);
    }
  },
  computed: {
    statusCell(row, cell) {
      return (row, cell) => {
        switch (this.playfield[row][cell]) {
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
  align-content: space-between;
  justify-content: center;
}
.playfield-cell {
  border: 1px solid green;
  width: 15px;
  height: 15px;
  margin: 10px;
}

.cell-alive {
  background-color: green;
}

.cell-dead {
  background-color: yellow;
}
</style>
