<template>
  <AppNavbar
    @bookmarkClicked="fetchBookmarkedCenters"
    @showAllCenters="fetchAllCenters"
    ref="navbar"
  />
  <div class="container-fluid">
    <div class="row">
      <NaverMap :centers="centers" />
      <CenterList
        :centers="centers"
        :showingBookmarkedCenters="showingBookmarkedCenters"
        @requireLogin="openLoginModal"
      />
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
      showingBookmarkedCenters: false,
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

  methods: {
    fetchBookmarkedCenters() {
      const userId = localStorage.getItem("userId");

      if (!userId) {
        this.centers = [];
        return;
      }

      axios
        .get(`http://localhost:9000/bookmarks/${userId}`)
        .then((response) => {
          this.centers = response.data;
          this.showingBookmarkedCenters = true;
        })
        .catch((error) => {
          console.error("북마크 목록을 가져오는데 실패했습니다:", error);
        });
    },

    fetchAllCenters() {
      axios
        .get("http://localhost:9000/centers")
        .then((response) => {
          this.centers = response.data;
          this.showingBookmarkedCenters = false;
        })
        .catch((error) => {
          console.error("센터 데이터를 가져오는데 실패했습니다:", error);
        });
    },

    openLoginModal() {
      this.$refs.navbar.showModal = true;
    }
  },
};
</script>
