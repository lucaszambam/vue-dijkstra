<template>
    <div class="panel-container">
        <div class="options-menu">
            <div class="menu-container" @click="toggleMenu">
                <div class="line-container">
                    <div class="line" v-for="i in 3"></div>
                </div>
            </div>
        </div>
        <div class="title-container">
            <h1>Vue Dijkstra</h1>
            <h3>An app to find the most cost-effective path beetween Brazilian Airports</h3>
        </div>
    </div>

    <div id="menu" class="hidden">
        <div class="top-area">
            <span class="title">Select the origin and destination</span>
            <div class="close-menu" @click="toggleMenu">
                <span class="close-span">X</span>
            </div>
        </div>

        <hr>

        <div class="control-area">
            <div class="input-container">
                <div>
                    <label>Origin: <input v-model="selectedOrigin" @change="onSelectOrigin" list="origin-airports"></label>
                    <datalist id="origin-airports">
                        <option v-for="airport in airports" :value="airport.id">{{ airport.name }}</option>
                    </datalist><br>
                </div>
                <span class="pipe">|</span>
                <div>
                    <label>Destination: <input v-model="selectedDestination" list="destination-airports"></label>
                    <datalist id="destination-airports">
                        <option v-for="airport in airports" :value="airport.id">{{ airport.name }}</option>
                    </datalist><br>
                </div>
            </div>

            <div class="button-container">
                <button @click="calculateBestRoute">Calculate best route</button>
            </div>
        </div>
    </div>
</template>
  
<script>
import airportsData from "/src/assets/json/airports.json";

export default {
    name: 'HeaderPanel',
    emits: ['calculate'],
    data() {
        return {
            airports: [],
            selectedOrigin: '',
            selectedDestination: '',
        };
    },
    mounted() {
        this.loadAirports();
    },
    methods: {
        toggleMenu() {
            const menu = document.getElementById('menu');
            menu.classList.toggle('hidden');
        },

        loadAirports() {
            this.airports = airportsData;
        },

        calculateBestRoute() {
            this.toggleMenu();
            this.$emit('calculate', {
                origin: this.selectedOrigin,
                destination: this.selectedDestination
            });
        },

        onSelectOrigin(event) {
            const originAirportId = event.currentTarget.value;
            if (originAirportId) {
                const originAirportFromOrign = document.querySelector(`#destination-airports option[value="${originAirportId}"]`);
                if (originAirportFromOrign) {
                    originAirportFromOrign.setAttribute('disabled', true);
                    return;
                }
            }

            const destinationAirports = document.getElementById('destination-airports');
            if (destinationAirports) {
                for (let i = 0; i < destinationAirports.children.length; i++) {
                    destinationAirports.children[i].removeAttribute('disabled');
                }
            }
        }
    }
}
</script>

<style scoped>
.panel-container {
    width: 100%;
    background-color: #010409;
    height: 8.5rem;
    border-bottom: 2px solid #21262d;
    display: flex;
}

.options-menu {
    display: flex;
    justify-content: center;
    align-items: center;
}

.menu-container {
    height: 32px;
    width: 32px;
    border: 1px solid #7d8590;
    border-radius: 0.4rem;
    cursor: pointer;
    position: absolute;
    left: 20px;
    transition: all .25s;
}

.menu-container:hover {
    border-color: #c2c2c2;
}

.menu-container:hover .line {
    background-color: #c2c2c2;
}

.line-container {
    display: flex;
    flex-direction: column;
    gap: 4px;
    justify-content: center;
    align-items: center;
    height: 100%;
}

.line {
    height: 2px;
    width: 20px;
    background-color: #7d8590;
    border-radius: 0.5rem;
    transition: all .25s;
}

.title-container {
    color: white;
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100%;
    width: 100%;
    gap: 5px;
}

.title-container h1 {
    font-style: italic;
    margin: 0;
}

.title-container h3 {
    font-weight: 300;
    margin: 0;
}

hr {
    width: 90%;
    background-color: #7D8590;
    border: none;
    height: 1px;
}

.pipe {
    color: #7D8590;
    font-weight: 900;
}

#menu {
    background-color: #161b22;
    width: 27rem;
    height: 100%;
    position: absolute;
    left: 0;
    border-radius: 0.5rem;
    display: grid;
    grid-template-rows: 5% min-content auto;
    box-shadow: 0 0 0 1px #30363d, 0 16px 32px rgba(1, 4, 9, 0.85);
    transition: left 0.3s ease;
    grid-gap: 15px;
}


#menu:not(.hidden) {
    animation: slideInMenu 0.35s forwards;
}

#menu.hidden {
    left: -27rem;
    animation: slideOutMenu 0.35s forwards;
}

.top-area {
    color: white;
    display: flex;
    padding: 20px;
    position: relative;
}

.top-area .title {
    margin-top: 6px;
    margin-left: 20px;
}

.close-menu {
    height: 35px;
    width: 40px;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 0.4rem;
    transition: all .2s;
    position: absolute;
    right: 10px;
    cursor: pointer;
}

.close-menu:hover {
    background-color: #292f36;
}

.close-menu:hover .close-span {
    color: white;
}

.close-span {
    font-family: cursive;
    font-size: 16px;
    color: #7D8590;
    font-weight: 700;
    cursor: pointer;
    transition: all .2s;
    user-select: none;
}

.control-area {
    padding: 0 40px;
    color: white;
}

.input-container {
    display: flex;
    justify-content: space-between;
}

input {
    width: 50px;
    height: 15px;
    padding: 5px;
    background-color: #21262d;
    color: white;
    border: 1px solid #7d8590;
    border-radius: 0.4rem;
    font-weight: 700;
    font-family: monospace;
    font-size: 1rem;
    text-align-last: center;
}

button {
    height: 27px;
    border-radius: 0.4rem;
    background-color: #21262d;
    border: 1px solid #7d8590;
    color: #c2c2c2;
    font-family: 'Roboto Mono';
    font-size: 16px;
    padding: 15px;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    transition: all .2s;
}

button:hover {
    border-color: #c2c2c2;
    color: white;
}

.button-container {
    padding-top: 15px;
    display: flex;
    justify-content: center;
}

@keyframes slideInMenu {
    0% {
        left: -27rem;
    }

    100% {
        left: 0;
    }
}

@keyframes slideOutMenu {
    0% {
        left: 0;
    }

    100% {
        left: -27rem;
    }
}
</style>