<template>
<button id="remove" class="btn btn-danger" :disabled="!this.itemsSelected.length > 0" @click.prevent="emitDeleteRecord()">
  Delete
</button>
<Vue3EasyDataTable
  v-model:items-selected="itemsSelected"
  fixed-checkbox
  :headers="headers"
  :items="records"
  :rows-per-page="10"
/>
</template>

<script>
import Vue3EasyDataTable from 'vue3-easy-data-table';
import 'vue3-easy-data-table/dist/style.css';

export default {
    props : ['records'],
    emits: ["deleteRecord"],
    data: () => ({
      itemsSelected: [],
      headers: [
        { text: "Place Name", value: "name" },
        { text: "Timezone", value: "timeZone"},
        { text: "Local Time", value: "localTime"},
      ],
    }),
    components: { Vue3EasyDataTable },
    methods: {
      checkAll() {
        let all = document.getElementById("checkall")
        all.checked = !all.checked
        console.log(document.getElementById('select-item'))
        document.getElementById('select-item').prop('checked', true)
      },
      emitDeleteRecord() {
        this.$emit('deleteRecord', this.itemsSelected)
        this.itemsSelected = []
      }
    },
}
</script>
<style>
  .shadow {
    box-shadow: none !important
  }
</style>