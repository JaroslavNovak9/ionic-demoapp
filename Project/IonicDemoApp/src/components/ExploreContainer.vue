<template>
  <div class="weather-app">
        <div class="weather-info">
            <h1>City, Country</h1>
            <p>Cloudy</p>
            <h2>20°C</h2>
        </div>
    </div>
</template>

<script setup lang="ts">
defineProps({
  name: String,
});
</script>

<style scoped>
/* Reset some default styles */
body, h1, h2, p {
    margin: 0;
    padding: 0;
}

body {
    font-family: "Helvetica Neue", sans-serif;
    background-color: #EFEFEF;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.weather-app {
    background-color: #FFFFFF;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
    padding: 20px;
    margin: 15px 0 0 0;
    text-align: center;
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.weather-info {
    flex: 1;
}

h1 {
    font-size: 28px;
    color: #333333;
    margin-bottom: 5px;
}

p {
    font-size: 18px;
    color: #555555;
}

h2 {
    font-size: 36px;
    color: #333333;
    margin-top: 5px;
}

.weather-icon img {
    max-width: 100px;
    margin: 10px auto;
}

</style>
