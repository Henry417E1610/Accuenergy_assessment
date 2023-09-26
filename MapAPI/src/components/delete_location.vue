<script>
export function delete_location(item, markers){
    if (item.selected) {
      const { lat, lng } = item;
      const index = markers.findIndex(marker => marker.getPosition().equals(new google.maps.LatLng(lat, lng)));
  
      if (index !== -1) {
        markers[index].setMap(null);
        markers[index].setVisible(false);
        markers[index].setPosition(null);
        markers[index] = null;
        markers.splice(index, 1);
      }
    }
}

</script>