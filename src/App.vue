<template>
  <div class="mt-20">
    <input
      type="text"
      v-model.lazy="city"
      placeholder="Search ..."
      class="w-96 h-10 bg-gray-100 text-lh outline-none px-4 py-2 rounded-tr-xl rounded-bl-xl font-semibold"
    />
    <div class="mt-10 text-center text-gray-50">
      <h1 class="text-2xl font-bold">{{ name }},{{ region }}</h1>
      <p class="text-lg italic mt-4">{{ date }}</p>
      <div class="bg-gray-100 py-10 px-4 bg-opacity-50 rounded-2xl mt-10">
        <span class="text-9xl font-extrabold">{{ temp }}°C</span>
      </div>
      <p v-if="clouds > 0" class="font-bold text-3xl italic mt-5">Cloud</p>
    </div>
  </div>
</template>

<script>
import { onMounted, reactive, ref, toRefs, watch } from "@vue/runtime-core";
import axios from "axios";

export default {
  name: "App",
  setup() {
    const api_key = process.env.VUE_APP_API_KEY;
    const url = "https://api.weatherapi.com/v1/current.json";
    const city = ref("London");
    const data = reactive({
      name: "",
      region: "",
      temp: 0,
      date: "",
      clouds: 0,
    });
    onMounted(() => {
      if (data.temp < 15)
        document.getElementById("app").classList.add("cold-background");
    });

    function getData() {
      const bodyParameters = {
        params: {
          key: api_key,
          q: city.value,
        },
      };
      axios.get(url, bodyParameters).then((res) => {
        data.name = res.data.location.name;
        data.region = res.data.location.region;
        data.temp = res.data.current.temp_c;
        data.date = res.data.location.localtime;
        data.clouds = res.data.current.cloud;
        if (data.temp > 15) {
          document.getElementById("app").classList.add("hot-background");
          document.getElementById("app").classList.remove("cold-background");
        } else {
          document.getElementById("app").classList.add("cold-background");
          document.getElementById("app").classList.remove("hot-background");
        }
      });
    }

    getData();

    watch(city, () => {
      getData();
    });

    return {
      city,
      ...toRefs(data),
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  display: flex;
  justify-content: center;
  background-repeat: no-repeat;
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
  height: 100vh;
}
.cold-background {
  background-image: url("./assets/cold-bg.jpg");
}
.hot-background {
  background-image: url("./assets/warm-bg.jpg");
}
</style>
