<script setup>
import locationImg from "../assets/location-sign.svg" 
import { locate } from "./geo_location.vue";
import { delete_location } from "./delete_location.vue";
import { add_location } from "./add_location.vue";
import { createApp } from 'vue';
import { library } from '@fortawesome/fontawesome-svg-core';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faSearch } from '@fortawesome/free-solid-svg-icons';
library.add(faSearch);
</script>

<template>
  <div>
      
      <div id="map"></div>

      <div class="Search_row">
       <input v-model="searchQuery" @keyup.enter="handleSearch" type="text" placeholder="Input location" class="Input_row" />
        <div class="row"><button type="button" class="Button" @click="handleSearch"><font-awesome-icon icon="fa-solid fa-search" /></button></div>
      </div>
  </div>
  
</template>



<script>

export default {
  props: {
     deleteItem: {
      type: Array,
      default: () => []
    },
     apiKey: {
      type: String, 
      required: true 
    }
  },
  data() {
    return {
      map: null,
      markers: [],
      searchQuery: "",
    };
  },
  mounted() {

    const script = document.createElement("script");
    script.src = `https://maps.googleapis.com/maps/api/js?key=${this.apiKey}&libraries=places&callback=initApp`;
    script.defer = true;
    script.async = true;
    // Load Google Maps API
    const load_map = new Promise((resolve, reject) => {
      script.onload = resolve; // Resolve the promise when the script is loaded
      script.onerror = reject; // Reject the promise if there's an error loading the script
    });

  document.head.appendChild(script);

  load_map.then(() => {
    this.initMap(); // Call this function when map is loaded
  }).catch(() => {
    console.error("Error loading Google Maps API.");
  });

  },
  methods: {

    initMap() {

      this.map = new google.maps.Map(document.getElementById("map"), {
          center: { lat: 53.42746873375151, lng: -113.68162904429436 },
          zoom: 15,
      });

       

      this.infoWindow = new google.maps.InfoWindow();

      const geo_locate_button = document.createElement("img");
       geo_locate_button.src = locationImg; 
       geo_locate_button.alt = "Location"; 
       geo_locate_button.style.width = "28px"; 
       geo_locate_button.style.height = "28px"; 
       geo_locate_button.style.borderRadius = "50%"; 
       geo_locate_button.style.padding = "5px"; 
       geo_locate_button.style.margin = "5px 15px"; 
       geo_locate_button.style.backgroundColor = "#fff"; 
       geo_locate_button.style.cursor = "pointer"; 
     

      this.map.controls[google.maps.ControlPosition.TOP_RIGHT].push(geo_locate_button);

      // To track current location
      geo_locate_button.addEventListener("click", () => {
          locate(this.infoWindow, this.map, this.markers);
        });

      google.maps.event.addListenerOnce(this.map, "idle", () => {
        this.removeMarkers();
      });

    },
    handleSearch() {
      if (!this.searchQuery){
        alert('Please enter a proper location');
        return;
      } 

      let service = new google.maps.places.PlacesService(this.map);
      service.findPlaceFromQuery({ query: this.searchQuery, fields: ["name","geometry"] }, (results, status) => {
        if (status === google.maps.places.PlacesServiceStatus.OK && results && results.length > 0) {
         
          for (let i = 0; i < results.length; i++) {       
            this.createMarker(results[i]);
          }
        
          this.map.setCenter(results[0].geometry.location);

          const lat = results[0].geometry.location.lat();
          const lng = results[0].geometry.location.lng();
          
          this.getTimeZone(lat, lng);

          const search_results = results.map((result) => ({
            name: result.name,
            lat: result.geometry.location.lat(),
            lng: result.geometry.location.lng(),
          }));
        
          this.$emit('searchResults', search_results);
        }
      });
    },
  
    createMarker(place) {
      add_location(place,this.map,this.markers);
    },

removeMarkers() {

  this.deleteItem.forEach(item => {
    delete_location(item,this.markers);
  });

  },

  
  getTimeZone(lat, lng) {
   
    const url = `https://api.geoapify.com/v1/geocode/reverse?lat=${lat}&lon=${lng}&format=json&apiKey=74c70dab588e4a969dbddd7eb27d728a`;

       return fetch(url)
      .then(resp => resp.json())
      .then((result) => {
      
        if (result.results.length > 0) {
          const timezoneinfo = result.results[0].timezone;
          this.$emit('timezone', timezoneinfo);
        } else {
          console.log("No location found");
        }
      });
  },
    
    handleLocationError(browserHasGeolocation, infoWindow, pos) {
        infoWindow.setPosition(pos);
        infoWindow.setContent(
          browserHasGeolocation
            ? "Error: The Geolocation service failed."
            : "Error: Your browser doesn't support geolocation."
        );
        infoWindow.open(map);
    },


  },

   watch: {
    deleteItem: {
      handler(newValue) {
        if(this.deleteItem.length !== 0){
           this.removeMarkers();
        }
      },
      immediate: true, 
    },
  },

};
</script>


<style scoped>
#map {
  width: 90vw;
  height: 500px;
}

html,
body {
  height: 100%;
  margin: 0;
  padding: 0;
}


.custom-location-icon {
    width: 24px;
    height: 24px;
    background-color: #fff;
    border-radius: 12px;
    margin: 10px;
    cursor: pointer;
}

.Search_row{
  margin: 10px 0;
  display:flex;
}

.row{
     margin-left: -8px;
}

.Input_row{
    width: 40%;
    padding: 10px 20px 10px 20px;
    min-width: 100px;
    min-height: 40px;
    outline: none;
    border: none;
    border-radius: 4px;
    box-shadow: rgba(0, 0, 0, 0.3) 0px 1px 4px -1px;
}

.Button{
    min-width: 45px;
    min-height: 45px;
    padding: 8px;
    background-color: blue;
    border-radius: 4px;
    color: #fff;
    border: none;
    outline: none;
    margin-left: 8px;
    margin-right: 8px;
    font-size: 1.8vh;
    font-weight: bold;
    white-space: nowrap;
    margin: 0 5px;
    cursor: pointer;
}

.Button:hover{
  background-color: #fff;
  border: 2px solid blue;
  color: blue;
}
</style>