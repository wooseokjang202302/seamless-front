<template>
  <div id="centerList" class="col-3">
    <ul>
      <li v-for="center in centers" :key="center.id">
        <h5 class="center-content">{{ center.name }}</h5>
        <p class="center-content">주소: {{ center.address }}</p>
        <p class="center-content">연락처: {{ center.tel }}</p>
        <a v-if="center.homepage" class="center-content" :href="center.homepage"
          >홈페이지</a
        >
        <button
          v-if="isBookmarked(center.id)"
          @click="deleteBookmark(center.id)"
          class="btn btn-outline-danger bookmark-btn"
        >
          북마크 삭제
        </button>
        <button
          v-else
          @click="addBookmark(center.id)"
          class="btn btn-outline-primary bookmark-btn"
        >
          북마크 추가
        </button>
      </li>

      <li v-if="centers.length === 0">
        <p class="center-content">해당 지역에 센터 정보가 없습니다.</p>
        <p class="center-content">
          공유하시고픈 센터가 있다면 test@test.com으로 센터 정보를 공유해주세요.
        </p>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: {
    centers: Array,
    showingBookmarkedCenters: Boolean,
  },

  data() {
    return {
      bookmarkedCenters: [],
    };
  },

  mounted() {
    this.fetchBookmarked();
  },

  methods: {
    addBookmark(centerId) {
      const userId = localStorage.getItem("userId");

      if (!userId) {
        this.$emit('requireLogin');
        return;
      }

      const bookmarkData = {
        userId: userId,
        centerId: centerId,
      };

      axios
        .post("http://localhost:9000/bookmarks", bookmarkData)
        .then((response) => {
          if (!this.bookmarkedCenters.includes(centerId)) {
            this.bookmarkedCenters.push(centerId);
          }
        })
        .catch((error) => {
          console.error("북마크 추가 실패:", error);
        });
    },

    deleteBookmark(centerId) {
      const userId = localStorage.getItem("userId");

      if (!userId) {
        this.$emit('requireLogin');
        return;
      }

      axios
        .delete(`http://localhost:9000/bookmarks/${userId}/${centerId}`, {
          data: { userId: userId },
        })
        .then((response) => {
          const index = this.bookmarkedCenters.indexOf(centerId);
          if (index !== -1) {
            this.bookmarkedCenters = [
              ...this.bookmarkedCenters.slice(0, index),
              ...this.bookmarkedCenters.slice(index + 1),
            ];
          }
        })
        .catch((error) => {
          console.error("북마크 삭제 실패", error);
        });
    },

    fetchBookmarked() {
      const userId = localStorage.getItem("userId");
      if (userId) {
        axios
          .get(`http://localhost:9000/bookmarks/${userId}`)
          .then((response) => {
            this.bookmarkedCenters = response.data.map((center) => center.id);
          })
          .catch((error) => {
            console.error("북마크 목록을 가져오는데 실패했습니다:", error);
          });
      }
    },
    isBookmarked(centerId) {
      return this.bookmarkedCenters.includes(centerId);
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
  right: 10px;
  bottom: 10px;
}
</style>
