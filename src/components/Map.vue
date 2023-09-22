<template>
    <div class="map-container">
        <div id="map"></div>
    </div>
</template>
  
<script>
import airportsData from "/src/assets/json/airports.json";
import brazilBoundaries from "/src/assets/json/brazilboundaries.json";

export default {
    name: 'Map',
    mounted() {
        this.initMap();
    },
    methods: {
        initMap() {
            const airports = airportsData;
            const map = L.map('map').setView([-14.235, -51.925], 4);
            L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}.png').addTo(map);

            const brazilBoundaryGeoJSON = brazilBoundaries;

            var brazilBoundaryLayer = L.geoJSON(brazilBoundaryGeoJSON, {
                style: {
                    color: "#010409", 
                    weight: 2,    
                    opacity: 1
                }
            }).addTo(map);

            const bounds = L.latLngBounds(L.latLng(-33.750, -74.910), L.latLng(5.271, -32.794));
            map.setMaxBounds(bounds);
            map.fitBounds(brazilBoundaryLayer.getBounds());

            const airportCluster = L.markerClusterGroup({
                disableClusteringAtZoom: 2
            });

            for (let i = 0; i < airports.length; i++) {
                const currentAirport = airports[i];
                const marker = L.marker([currentAirport.location.lat, currentAirport.location.long]).bindPopup(currentAirport.name);
                airportCluster.addLayer(marker);
            }

            map.addLayer(airportCluster);
        },
    },
}
</script>
  
<style scoped>
.map-container {
    display: flex;
    justify-content: center;
    align-items: center;
}

#map {
    width: 45rem;
    height: 45rem;
    border-radius: .5rem;
}
</style>
  