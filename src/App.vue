<template>
  <section id="rows-list">
    <ul class="rows" id="rows-section">
      <li class="row" v-for="row, index in rows" :key="index">
        <ul class="cells" :id="index.toString()">
          <li class="cell" v-for="cell, idx in row" :key="idx">{{ cell.num }}</li>
        </ul>
      </li>
    </ul>
  </section>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

const minRowsAmount = 101
const maxRowsAmount = 301
const minCellsAmount = 11
const maxCellsAmount = 21

let rows = ref([[{ num: 0 }]])

let cellsIndexesToChangeValues = ref([])

const fillCells = () => {
  const cellsAmount = Math.round(Math.random() * (maxCellsAmount - minCellsAmount) + minCellsAmount)
  const cells = Array.from({ length: cellsAmount }, () => {
    return { num:  Math.floor(Math.random() * 100) }
  })
  return cells
}

const fillRows = () => {
  const rowsAmount = Math.round(Math.random() * (maxRowsAmount - minRowsAmount) + minRowsAmount)
  const rowsArr = Array.from({ length: rowsAmount }, fillCells)
  rows.value = rowsArr
}

fillRows()

onMounted(() => {
  const observableCells = document.querySelectorAll('.cells')
  observableCells.forEach(cell => {
    const observer = new IntersectionObserver((entry) => {
      if (entry[0].intersectionRatio === 1) {
        cellsIndexesToChangeValues.value.push(entry[0].target.id)
      } else {
        if (cellsIndexesToChangeValues.value.length) {
          cellsIndexesToChangeValues.value = cellsIndexesToChangeValues.value.filter(cell => cell !== entry[0].target.id)
        }
      }
    }, { root: null, rootMargin: '0px', threshold: 1 })

    observer.observe(cell)
  })

  const updateCellsValues = () => {
    if (!cellsIndexesToChangeValues.value.length) {
      return
    }
    cellsIndexesToChangeValues.value.forEach(idx => {
      const randomCellToUpdate = Math.floor(Math.random() * rows.value[idx].length)
      rows.value[idx][randomCellToUpdate].num = Math.floor(Math.random() * 100)
    })
  }

  setInterval(updateCellsValues, 1000)
})
</script>