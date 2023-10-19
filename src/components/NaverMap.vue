<template>
  <div class="col-9" id="map" ref="map"></div>
</template>

<script>
export default {
  name: "NaverMap",
  props: {
    centers: Array,
  },
  data() {
    return {
      naverMap: null,
    };
  },
  mounted() {
    this.initMap();
    this.addMarkers();
  },
  methods: {
    initMap() {
      this.naverMap = new naver.maps.Map(this.$refs.map, {
        center: new naver.maps.LatLng(37.5665, 126.978),
        zoom: 10,
      });
    },
    addMarkers() {
      if (!this.centers || !this.naverMap) return;
      this.centers.forEach((center) => {
        if (center.mapx && center.mapy) {
          new naver.maps.Marker({
            map: this.naverMap,
            position: new naver.maps.LatLng(center.mapy, center.mapx),
          });
        }
      });
    },
  },
  watch: {
    centers(newCenters) {
      this.addMarkers();
    },
  },
};
</script>

<style scoped>
#map {
  width: 100%;
  height: calc(100vh - 95px);
}
</style>
