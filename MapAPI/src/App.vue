<script setup>
import MapComponent from './components/display_map.vue'
import TableComponent from './components/display_table.vue'
</script>

<template>
  <div>
    <MapComponent :apiKey="apiKey" @timezone="timezoneResult" @searchResults="searchResult" :deleteItem="deleteLocations" />
   <TableComponent :locations="searchedLocations" @delete="deleteSelected" :timezoneTable="timezoneinfo" />
    <!-- <div v-if="localTime">Local Time: {{ localTime }}</div>  -->
  </div>
</template>

<script>
export default {
  components: {
    MapComponent,
    TableComponent
  },
  data() {
    return {
      apiKey:"AIzaSyDFCiWdUfRPzCyOiGJJMHIUZqKkFUoH2-8",
      localTime: null,
      searchedLocations: [],
      timezoneinfo: [],
      deleteLocations : [],
    }
  },
  methods: {
    searchResult(results) {
      this.searchedLocations = results;
    },
    timezoneResult(info) {
      this.timezoneinfo = info;
    },
    deleteSelected(selectedLocations) {
      this.deleteLocations = selectedLocations;
     
    }
  }
}
</script>
