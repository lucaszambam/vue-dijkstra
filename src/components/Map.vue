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
                    weight: 1,
                    opacity: 0.5
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

            this.validateAirportsData(airports);

            for (let i = 0; i < airports.length; i++) {
                const currentAirport = airports[i];
                const markerPopupContent = `<strong>${currentAirport.id}</strong><br>
                                            <span>${currentAirport.name}</span><br>
                                            <ul>
                                                <li><strong>Latitude</strong>: ${currentAirport.location.lat}</li>
                                                <li><strong>Longitude</strong>: ${currentAirport.location.lng}</li>
                                            </ul>`;

                currentAirport.destinations.map(function (id) {
                    const destinationAirport = airports.find(airport => airport.id === id);

                }, currentAirport);

                for (let j = 0; j < currentAirport.destinations.length; j++) {
                    const destinationAirport = airports.find(airport => airport.id === currentAirport.destinations[j]);
                    this.addRoute(currentAirport, destinationAirport, map);
                }

                const marker = L.marker([currentAirport.location.lat, currentAirport.location.lng], {
                    icon: new L.DivIcon({
                        className: 'marker-icon',
                        html: `<div class="marker-container">
                                    <div class="marker-image"></div>
                                    <span class="marker-span">${currentAirport.id}</span>
                                </div>`
                    })
                }).bindPopup(markerPopupContent);

                airportCluster.addLayer(marker);
            }

            map.addLayer(airportCluster);
        },

        addRoute(origin, destination, map) {
            const polyline = L.polyline([[origin.location, destination.location]], {
                color: '#21262d',
                weight: 1,
                opacity: 0.25
            }).addTo(map);

            const path = polyline._path;
            path.classList.add('polyline-route');
        },

        validateAirportsData(airports) {
            for (const originAirport of airports) {
                for (const destinationCode of originAirport.destinations) {
                    const destinationAirport = airports.find(airport => airport.id === destinationCode);

                    if (!destinationAirport) {
                        console.error(`O aeroporto de destino ${destinationCode} não foi encontrado.`);
                    } else if (!destinationAirport.destinations.includes(originAirport.id)) {
                        console.error(`O aeroporto ${originAirport.id} não está listado como destino em ${destinationCode}.`);
                    }
                }
            }
        }
    },
}
</script>
  
<style>
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

.marker-container {
    display: grid;
    grid-template-rows: 1fr 1fr;
    width: max-content;
    text-align: center;
}

.marker-image {
    border: 1px solid white;
    border-top-right-radius: 0.4rem;
    border-top-left-radius: 0.4rem;
    background-color: #21262d;
    border: 1px solid #7d8590;
    background-image: url(/src/assets/svg/plane.svg);
    background-repeat: no-repeat;
    background-position: center;
    padding: 1px;
}

.marker-span {
    background: #21262d;
    color: white;
    height: max-content;
    border: 1px solid #7d8590;
    border-bottom-left-radius: 0.4rem;
    border-bottom-right-radius: 0.4rem;
    border-top: none;
    padding: 2px;
    font-family: 'Roboto Mono';
}

.marker-container:hover {
    transform: scale(1.2);
    transition: all .25s;
}

.leaflet-popup-content {
    font-family: 'Roboto Mono';
    font-weight: 500;
}
</style>