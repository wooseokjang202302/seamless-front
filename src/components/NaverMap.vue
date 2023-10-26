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
      markers: [],
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

      // 기존 마커 제거
      this.markers.forEach((marker) => {
        marker.setMap(null);
      });
      this.markers = [];

      let bounds = new naver.maps.LatLngBounds(); // 경계 객체 생성

      this.centers.forEach((center) => {
        if (center.mapx && center.mapy) {
          const position = new naver.maps.LatLng(center.mapy, center.mapx);
          const marker = new naver.maps.Marker({
            map: this.naverMap,
            position: position,
          });
          this.markers.push(marker);
          bounds.extend(position); // 생성한 마커의 지점을 경계에 추가
        }
      });

      if (this.markers.length > 1) {
        this.naverMap.fitBounds(bounds); // 모든 마커가 보이게 설정
      } else if (this.markers.length === 1) {
        this.naverMap.setCenter(bounds.getCenter()); // 마커가 하나일 경우 해당 지점을 중앙으로 설정
        this.naverMap.setZoom(15); // 줌 레벨 설정
      }
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
