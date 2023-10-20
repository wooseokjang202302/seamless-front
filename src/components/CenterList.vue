<template>
  <div id="centerList" class="col-3">
    <ul>
      <!-- 데이터를 props로 받아 사용 -->
      <li v-for="center in centers" :key="center.id">
        <h5 class="center-content">{{ center.name }}</h5>
        <p class="center-content">주소: {{ center.address }}</p>
        <p class="center-content">연락처: {{ center.tel }}</p>
        <a v-if="center.homepage" class="center-content" :href="center.homepage"
          >홈페이지</a
        >
        <button
          @click="bookmarkCenter(center.id)"
          class="btn btn-outline-primary bookmark-btn"
        >
          북마크
        </button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: {
    centers: Array, // centers 데이터를 props로 받음
  },

  methods: {
    bookmarkCenter(centerId) {
      const userId = localStorage.getItem("userId");

      if (!userId) {
        alert("로그인이 필요한 기능입니다.");
        return;
      }

      const bookmarkData = {
        userId: userId,
        centerId: centerId,
      };

      axios
        .post("http://localhost:9000/bookmarks", bookmarkData)
        .then((response) => {
          alert("북마크가 추가되었습니다!");
          // 이후 필요한 로직을 여기에 추가 (예: 북마크 목록 업데이트, 알림 등)
        })
        .catch((error) => {
          console.error("Bookmark error:", error);
          alert("북마크 추가에 실패했습니다. 다시 시도해주세요.");
        });
    },
  },
};
</script>

<style scoped>
#centerList {
  height: calc(100vh - 95px);
  overflow-y: auto;
}

.center-content {
  overflow: hidden;
}

ul {
  list-style-type: none;
  padding-left: 0;
}

li {
  border: 1px solid #ddd;
  margin: 10px 0;
  padding: 8px;
  border-radius: 4px;
  position: relative;
}

li:first-child {
  margin-top: 0;
}

li:last-child {
  margin-bottom: 0;
}

.bookmark-btn {
  position: absolute;
  right: 10px; /* 오른쪽 패딩 */
  bottom: 10px; /* 아래쪽 패딩 */
}
</style>
