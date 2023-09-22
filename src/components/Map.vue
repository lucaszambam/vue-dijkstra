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
            const map = L.map('map').setView([-14.235, -51.925], 4);
            L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}.png', {
                minZoom: 4,
                maxZoom: 8,
            }).addTo(map);
            
            this.addBrazilBoundaries(map);
            this.addMarkers(map);
        },

        addBrazilBoundaries(map) {
            const brazilBoundaryGeoJSON = brazilBoundaries;
            const brazilBoundaryLayer = L.geoJSON(brazilBoundaryGeoJSON, {
                style: {
                    color: "#010409",
                    weight: 2,
                    opacity: 1
                }
            }).addTo(map);

            const bounds = L.latLngBounds(L.latLng(-33.750, -74.910), L.latLng(5.271, -32.794));
            map.setMaxBounds(bounds);
            map.fitBounds(brazilBoundaryLayer.getBounds());
        },

        addMarkers(map) {
            const airports = airportsData;
            const airportCluster = L.markerClusterGroup({
                disableClusteringAtZoom: 2
            });

            for (let i = 0; i < airports.length; i++) {
                const currentAirport = airports[i];
                const markerPopupContent = `<strong>${currentAirport.id}</strong><br>
                                            <span>${currentAirport.name}</span><br>
                                            <ul>
                                                <li><strong>Latitude</strong>: ${currentAirport.location.lat}</li>
                                                <li><strong>Longitude</strong>: ${currentAirport.location.long}</li>
                                            </ul>`;
                const marker = L.marker([currentAirport.location.lat, currentAirport.location.long], {
                    icon: new L.DivIcon({
                        className: 'marker-icon',
                        html: `<div class="marker-container" style="display: grid; grid-template-rows: 1fr auto; width: 30px; text-align: center;">
                                    <img style="width: inherit; background-color: currentcolor; border: 1px solid white; border-top-right-radius: 0.4rem; border-top-left-radius: 0.4rem;" "class="marker-image" src="http://png-3.vector.me/files/images/4/0/402272/aiga_air_transportation_bg_thumb"/>
                                    <span style="background: black; color: white; height: max-content; border: 1px solid white; border-bottom-left-radius: 0.4rem; border-bottom-right-radius: 0.4rem; border-top: none;"class="marker-span">${currentAirport.id}</span>
                                </div>`
                    })
                }).bindPopup(markerPopupContent);
                
                airportCluster.addLayer(marker);
            }

            map.addLayer(airportCluster);
        }
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
  