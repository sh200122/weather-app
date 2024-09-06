<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>您目前正在预览此城市，点击“+”图标即可开始跟踪此城市。</p>
    </div>

    <!-- Weather Overview -->
    <div v-if="weatherData" class="flex flex-col items-center text-white mt-10">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-2xl mb-10 mt-10">
        {{
          new Date(weatherData.dt * 1000).toLocaleDateString("zh-CH", {
            weekday: "short",
            day: "2-digit",
            month: "long",
          })
        }}
        {{
          new Date(weatherData.dt * 1000).toLocaleTimeString("zn-CH", {
            timeStyle: "short",
          })
        }}
      </p>
      <p class="text-8xl mb-10">
        {{ Math.round(weatherData.main.temp) }}&deg;C
      </p>
      <p>大概 {{ Math.round(weatherData.main.feels_like) }} &deg;C</p>
      <p class="capitalize">
        {{ weatherData.weather[0].description }}
      </p>
      <img
        class="w-[150px] h-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
        alt=""
      />
    </div>

    <div
      v-if="weatherData"
      class="flex items-center gap-2 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>删除城市</p>
    </div>

    <div
      v-if="weatherData"
      class="flex items-center gap-2 mt-10 mb-10 text-white cursor-pointer duration-150 hover:text-blue-500"
      @click="back"
    >
      <i class="fa fa-arrow-left"></i>
      <p>返回</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const router = useRouter();
const weatherData = ref(null);

const getWeatherData = async () => {
  try {
    const response = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&appid=5abca9496c41be1c4a71087bc800ae6b&units=metric&lang=zh_cn`
    );

    const data = response.data;
    weatherData.value = data;
  } catch (err) {
    console.error("Failed to fetch weather data:", err);
  }
};

const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({ name: "home" });
};

const back = () => {
  router.push({ name: "home" });
};

getWeatherData();
</script>
