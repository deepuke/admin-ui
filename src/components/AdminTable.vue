<script setup>
import { AgGridVue } from 'ag-grid-vue3'
import { reactive, onMounted, ref } from 'vue'
import 'ag-grid-community/styles/ag-grid.css' // Core grid CSS, always needed
import 'ag-grid-community/styles/ag-theme-alpine.css' // Optional theme CSS

const gridApi = ref(null)
// Obtain API from grid's onGridReady event
const onGridReady = (params) => {
  gridApi.value = params.api
}

const rowData = reactive({}) // Set rowData to Array of Objects, one Object per Row

// Each Column Definition results in one Column.
const columnDefs = reactive({
  value: [{ field: 'name' }, { field: 'email' }, { field: 'role' }]
})

// DefaultColDef sets props common to all Columns
const defaultColDef = {
  sortable: true,
  filter: true,
  flex: 1
}

// Example load data from sever
onMounted(() => {
  fetch('https://geektrust.s3-ap-southeast-1.amazonaws.com/adminui-problem/members.json')
    .then((result) => result.json())
    .then((remoteRowData) => (rowData.value = remoteRowData))
})

const cellWasClicked = (event) => {
  // Example of consuming Grid Event
  console.log('cell was clicked', event)
}
const deselectRows = () => {
  gridApi.value.deselectAll()
}

const onFilterTextBoxChanged = (event) => {
  gridApi.value.setQuickFilter(event.target.value)
}
</script>
<template>
  <div class="adminTable">
    <input
      type="text"
      id="filter-text-box"
      placeholder="Filter..."
      v-on:input="onFilterTextBoxChanged"
    />

    <ag-grid-vue
      class="ag-theme-alpine"
      style="height: 550px"
      :columnDefs="columnDefs.value"
      :rowData="rowData.value"
      :defaultColDef="defaultColDef"
      rowSelection="multiple"
      animateRows="true"
      :pagination="true"
      :paginationPageSize="10"
      :paginationAutoPageSize="true"
      @cell-clicked="cellWasClicked"
      @grid-ready="onGridReady"
    >
    </ag-grid-vue>
    <button @click="deselectRows">deselect rows</button>
  </div>
</template>
<style lang="css">
.adminTable {
  min-width: 800px;
}
</style>
