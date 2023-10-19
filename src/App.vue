<template>
  <AppNavbar />
  <div class="container-fluid">
    <div class="row">
      <NaverMap :centers="centers" />
      <CenterList :centers="centers" />
    </div>
  </div>
</template>

<script>
import axios from "axios";
import AppNavbar from "./components/AppNavbar.vue";
import NaverMap from "./components/NaverMap.vue";
import CenterList from "./components/CenterList.vue";

export default {
  name: "App",
  components: {
    AppNavbar,
    NaverMap,
    CenterList,
  },
  data() {
    return {
      centers: [],
    };
  },
  mounted() {
    axios
      .get("http://localhost:9000/centers")
      .then((response) => {
        this.centers = response.data;
      })
      .catch((error) => {
        console.error("센터 데이터를 가져오는데 실패했습니다:", error);
      });
  },
};
</script>
