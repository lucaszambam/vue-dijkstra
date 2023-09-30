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
    props: ['route'],
    watch: {
        route: function (routeInput) {
            this.calculateCheapestPrice(routeInput.origin, routeInput.destination);
        }
    },
    mounted() {
        this.initMap();
    },
    methods: {
        initMap() {
            this.map = L.map('map').setView([-14.235, -51.925], 4);

            L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}.png', {
                minZoom: 4,
                maxZoom: 15
            }).addTo(this.map);

            this.addBrazilBoundaries(this.map);
            this.addMarkers(this.map);
        },

        addBrazilBoundaries() {
            const brazilBoundaryGeoJSON = brazilBoundaries;
            const brazilBoundaryLayer = L.geoJSON(brazilBoundaryGeoJSON, {
                style: {
                    color: "#010409",
                    weight: 1,
                    opacity: 0.5
                }
            }).addTo(this.map);

            const bounds = L.latLngBounds(L.latLng(-33.750, -74.910), L.latLng(5.271, -32.794));
            this.map.setMaxBounds(bounds);
            this.map.fitBounds(brazilBoundaryLayer.getBounds());
        },

        addMarkers() {
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

                for (let j = 0; j < currentAirport.destinations.length; j++) {
                    const destinationAirport = airports.find(airport => airport.id === currentAirport.destinations[j].id);
                    this.addRoute(currentAirport, destinationAirport, {
                        color: '#21262d',
                        weight: 1,
                        opacity: 0.25
                    });
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

            this.map.addLayer(airportCluster);
        },

        addRoute(origin, destination, options, popupContent = false) {
            const route = L.polyline([[origin.location, destination.location]], options).addTo(this.map);

            if (popupContent) {
                route.bindPopup(popupContent);
            }
        },

        calculateCheapestPrice(originId, destinationId) {
            const airports = airportsData;
            const shortestPath = {};

            airports.map((airport) => {
                shortestPath[airport.id] = {
                    cost: Infinity,
                    path: [],
                    flights: [],
                };
            });
            shortestPath[originId].cost = 0;

            const unvisited = new Set(Object.keys(shortestPath));

            while (unvisited.size > 0) {
                let currentId = null;
                let currentCost = Infinity;

                for (const id of unvisited) {
                    if (shortestPath[id].cost < currentCost) {
                        currentId = id;
                        currentCost = shortestPath[id].cost;
                    }
                }

                if (currentId === destinationId) {
                    // Found the destination, log the path and cost
                    console.log(`Shortest path from ${originId} to ${destinationId}:`);
                    console.log(shortestPath[destinationId].path.join(' -> '));
                    console.log(`Cost: ${shortestPath[destinationId].cost}`);

                    // Log all flights in the path
                    console.log("Flights:");
                    shortestPath[destinationId].flights.map((flight) => {
                        console.log(`From ${flight.origin.lat}, ${flight.origin.lng} to ${flight.destination.lat}, ${flight.destination.lng}`);
                        debugger;
                        const originLocation = {
                            location: {
                                lat: flight.origin.lat,
                                lng: flight.origin.lng
                            }
                        };

                        const destinationLocation = {
                            location: {
                                lat: flight.destination.lat,
                                lng: flight.destination.lng,
                            }
                        };

                        return {
                            originLocation,
                            destinationLocation,
                            routeOptions: {
                                color: '#41b883',
                                weight: 5,
                                opacity: 1
                            }
                        };
                    }).forEach((route) => {
                        this.addRoute(route.originLocation, route.destinationLocation, route.routeOptions, 'test');
                    });
                    break;
                }

                unvisited.delete(currentId);
                const currentAirport = airports.find(airport => airport.id === currentId);

                currentAirport.destinations.map((destination) => {
                    const neighborId = destination.id;
                    const neighborAirport = airports.find((airport) => airport.id === neighborId);
                    const tentativeCost = shortestPath[currentId].cost + destination.price;

                    if (tentativeCost < shortestPath[neighborId].cost) {
                        // Update shortest path, cost, and flights
                        shortestPath[neighborId].cost = tentativeCost;
                        shortestPath[neighborId].path = [...shortestPath[currentId].path, currentId];
                        shortestPath[neighborId].flights = [
                            ...shortestPath[currentId].flights,
                            {
                                origin: currentAirport.location,
                                destination: neighborAirport.location,
                            },
                        ];
                    }
                });
            }
        },

        validateAirportsData(airports) {
            for (const originAirport of airports) {
                for (const destination of originAirport.destinations) {
                    const destinationAirport = airports.find(airport => airport.id === destination.id);

                    if (!destinationAirport) {
                        console.error(`O aeroporto de destino ${destination.id} não foi encontrado.`);
                    } else if (!destinationAirport.destinations.find(destination => destination.id === originAirport.id)) {
                        console.error(`O aeroporto ${originAirport.id} não está listado como destino em ${destination.id}.`);
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
    height: 100%;
    width: 100%;
}

#map {
    max-width: 50rem;
    max-height: 45rem;
    width: 100%;
    height: 100%;
    border-radius: 0.5rem;
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