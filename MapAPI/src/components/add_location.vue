<script>
export function add_location(pos, map, marker){
    if (pos.geometry && pos.geometry.location){
        const mk = new google.maps.Marker({
            map: map, 
            position :pos.geometry.location,
            lat: pos.geometry.location.lat(),
            lng: pos.geometry.location.lng(),
        });
        google.maps.event.addListener(mk, "click", () => {
            info.setContent(pos.name || "");
            info.open(map); 
        });
        marker.push(mk);
    }
}

</script>