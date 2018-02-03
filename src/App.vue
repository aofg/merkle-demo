<template>
  <div id="app">
    <table>
      <tr v-for="(row, rowIndex) in master" :key="rowIndex">
        <td v-for="(cell, cellIndex) in row" :key="cellIndex">
          <button @click="increment(rowIndex, cellIndex)">
            {{ cell }}
          </button>
        </td>
      </tr>
    </table>
    <merkle-tree :branch='masterTree'></merkle-tree>
  </div>
</template>

<script>
import { sha256 } from 'js-sha256'
import MerkleTree from '@/components/MerkleTree'

class Branch {
  constructor (value) {
    if (Array.isArray(value)) {
      this.branches = value
      this.hash = sha256((value[0].hash + value[1].hash + value[2].hash + value[3].hash).toString())
    } else {
      this.hash = sha256(value.toString())
    }
  }
}

const COUNT = 8

export default {
  name: 'App',
  data () {
    const master = []
    const slave = []

    for (let row = 0; row < COUNT; row++) {
      master[row] = []
      slave[row] = []
      for (let cell = 0; cell < COUNT; cell++) {
        master[row][cell] = 0
        slave[row][cell] = 0
      }
    }
    return {
      master,
      slave
    }
  },

  computed: {
    masterTree () {
      const leafs = []
      for (let row = 0; row < COUNT; row++) {
        for (let cell = 0; cell < COUNT; cell++) {
          const value = this.master[row][cell]
          leafs.push(new Branch(value))
        }
      }

      while (leafs.length > 1) {
        const previous = leafs.length
        for (let index = 0; index < previous; index += 4) {
          const a = leafs[index + 0]
          const b = leafs[index + 1]
          const c = leafs[index + 2]
          const d = leafs[index + 3]

          leafs.push(new Branch([a, b, c, d]))
        }

        leafs.splice(0, previous)
      }

      return leafs[0]
    }
  },

  components: {
    MerkleTree
  },

  mounted () {
  },

  methods: {
    increment (row, cell) {
      this.$set(this.master[row], cell, this.master[row][cell] + 1)
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
