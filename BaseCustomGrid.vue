<script setup>
 
const emit = defineEmits(["select"]); // Define the emit event
// Define grid size
const rows = 8;
const cols = 8;
const selectedRows = ref(0);
const selectedCols = ref(0);
 
// Create an array of grid cells (initially unselected)
const gridCells = ref(Array(rows * cols).fill({ isSelected: false }));
 
// Method to highlight cells in a row and column pattern
const highlightCells = (index) => {
  // Calculate which row and column the hovered cell is in
  selectedRows.value = Math.floor(index / cols) + 1;
  selectedCols.value = (index % cols) + 1;
 
  gridCells.value = gridCells.value.map((_, i) => {
    const currentRow = Math.floor(i / cols) + 1;
    const currentCol = (i % cols) + 1;
 
    // Highlight if the current cell is in the hovered row or column
    return {
      isSelected:
        currentRow <= selectedRows.value && currentCol <= selectedCols.value,
    };
  });
};
 
// Method to select the current grid dimensions and emit them
const selectGrid = () => {
  if (selectedRows.value > 0 && selectedCols.value > 0) {
    emit("select", { rows: selectedRows.value, cols: selectedCols.value }); // Emit the selected rows and columns
  }
  resetGrid();
};
 
// Reset the grid when the user stops hovering
function resetGrid() {
  gridCells.value = gridCells.value.map(() => ({ isSelected: false }));
  selectedRows.value = 0;
  selectedCols.value = 0;
}
// onBeforeUnmount(()=>{
//     resetGrid()
// })
</script>

<template>
  <div class="grid-container" >
    <!-- @mouseleave="resetGrid" -->
    <!-- -->
    <div class="grid">
      <div
        v-for="(cell, index) in gridCells"
        :key="index"
        :class="['cell', cell.isSelected ? 'selected' : '']"
        @mouseover="highlightCells(index)"
        @click="selectGrid"
      ></div>
    </div>
    <div class="dimensions">{{ selectedRows }} × {{ selectedCols }}</div>
  </div>
</template>
 
 
<style scoped>
.grid-container {
  display: inline-block;
  position: relative;
}
 
.grid {
  display: grid;
  grid-template-columns: repeat(8, 20px);
  grid-template-rows: repeat(8, 20px);
}
 
.cell {
  border: 1px solid #ccc;
  width: 20px;
  height: 20px;
  cursor: pointer;
}
 
.selected {
  background-color: #3498db;
}
 
.dimensions {
  margin-top: 10px;
  font-size: 0.85rem;
}
</style>
