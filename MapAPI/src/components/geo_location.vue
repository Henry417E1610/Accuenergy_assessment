<script>
import { add_location } from "./add_location.vue";
    export function locate(info, map, marker){
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(
                (position) => {
                    const pos = {
                        lat: position.coords.latitude,
                        lng: position.coords.longitude,
                    };
                
                    info.setPosition(pos);
                    info.setContent("Location found.");
                    info.open(map);
                    map.setCenter(pos);
                    map.setZoom(15);
                    add_location(pos,map,marker)
                    
                },
                () => {
                    handleLocationError(true, info, map.getCenter());
                }
            );
        } else {
            // Browser doesn't support Geolocation
            handleLocationError(false, info, map.getCenter());
        }
    }    
</script>


