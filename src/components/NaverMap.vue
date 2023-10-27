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
      isInitialLoad: true,
      didSetUserLocation: true
    };
  },
  mounted() {
    this.initMap();
  },
  methods: {
    initMap() {
    const defaultLocation = new naver.maps.LatLng(37.5665, 126.978); // 기본 위치: 서울

    // 사용자의 현재 위치 획득
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(
        (position) => {
          const userLocation = new naver.maps.LatLng(position.coords.latitude, position.coords.longitude);
          this.initializeNaverMap(userLocation); // 사용자의 위치로 지도 초기화
        },
        (error) => {
          console.error("Geolocation Error:", error);
          this.initializeNaverMap(defaultLocation); // 에러 발생 시 기본 위치로 지도 초기화
        }
      );
    } else {
      // Geolocation을 지원하지 않는 브라우저일 경우
      this.initializeNaverMap(defaultLocation); // 기본 위치로 지도 초기화
    }
  },

  initializeNaverMap(center) {
    this.naverMap = new naver.maps.Map(this.$refs.map, {
      center: center,
      zoom: 15,
    });
    this.initializeMarker();
    if (!this.didSetUserLocation) {
      this.naverMap.setCenter(center); // 사용자의 위치로 지도 중앙 설정
      this.naverMap.setZoom(15);       // 초기 줌 레벨 설정
      this.didSetUserLocation = true;  // 사용자 위치 설정 완료
    }
  },

  initializeMarker() {
    
    if (!this.centers || !this.naverMap) return;

    this.markers = [];

    this.centers.forEach((center) => {
      if (center.mapx && center.mapy) {
        const position = new naver.maps.LatLng(center.mapy, center.mapx);
        const marker = new naver.maps.Marker({
          map: this.naverMap,
          position: position,
        });
        this.markers.push(marker);
      }
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

    if (this.didSetUserLocation) {
      if (this.markers.length > 1) {
        this.naverMap.fitBounds(bounds); // 모든 마커가 보이게 설정
      } else if (this.markers.length === 1) {
        this.naverMap.setCenter(bounds.getCenter()); // 마커가 하나일 경우 해당 지점을 중앙으로 설정
      }
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
