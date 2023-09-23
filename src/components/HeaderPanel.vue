<template>
    <div class="panel-container">
        <div class="options-menu">
            <div class="menu-container">
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
</template>
  
<script>

export default {
    name: 'HeaderPanel',
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
</style>
  