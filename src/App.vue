<template>
  <AppNavbar
    @bookmarkClicked="fetchBookmarkedCenters"
    @showAllCenters="fetchAllCenters"
    @filterChanged="onFilterChanged"
    ref="navbar"
  />
  <div class="container-fluid">
    <div class="row">
      <NaverMap :centers="filteredCenters" />
      <CenterList
        :centers="filteredCenters"
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
      filter: {
        do_si: "전체",
        si_gun_gu: "전체"
      }
    };
  },

  created() {
    this.fetchAllCenters();
  },

  computed: {
    filteredCenters() {
      if (this.filter.do_si === "전체") {
        return this.centers;
      }

      return this.centers.filter(center => {
        return center.do_si === this.filter.do_si && (this.filter.si_gun_gu === "전체" || center.si_gun_gu === this.filter.si_gun_gu);
      });
    }
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

    onFilterChanged(filter) {
      this.filter.do_si = filter.do_si;
      this.filter.si_gun_gu = filter.si_gun_gu;
    },

    fetchFilteredCenters(do_si, si_gun_gu) {
      const filteredCenters = this.centers.filter(center => center.do_si === do_si && center.si_gun_gu === si_gun_gu);
      this.centers = filteredCenters;

    openLoginModal() {
      this.$refs.navbar.showModal = true;
    }
  },
};
</script>
