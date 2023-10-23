<template>
  <div>
    <nav class="navbar navbar-expand-md custom-navbar mb-4">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">Top navbar</a>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarCollapse"
          aria-controls="navbarCollapse"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarCollapse">
          <ul class="navbar-nav me-auto mb-2 mb-md-0">
            <li class="nav-item">
              <a class="nav-link active" aria-current="page" href="#">Home</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#">Link</a>
            </li>
            <li class="nav-item">
              <a class="nav-link disabled" aria-disabled="true">Disabled</a>
            </li>
          </ul>
          <!-- ml-auto 클래스를 추가하여 오른쪽 정렬 -->
          <div v-if="loggedIn" class="btn-group ml-auto">
            <button
              @click="toggleView"
              :class="isBookmarkView ? 'btn btn-primary' : 'btn btn-success'"
            >
              {{ isBookmarkView ? "전체보기" : "북마크" }}
            </button>
            <button @click="logout" class="btn btn-danger">로그아웃</button>
          </div>
          <button
            v-else
            @click="showModal = true"
            class="btn btn-primary ml-auto"
          >
            로그인
          </button>
        </div>
      </div>
    </nav>

    <!-- 로그인 모달창 -->
    <div v-if="showModal" class="modal show d-block">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">로그인</h5>
            <button @click="showModal = false" type="button" class="close">
              <span>&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <input
              type="text"
              class="form-control"
              placeholder="이메일"
              v-model="email"
            />
            <input
              type="password"
              class="form-control mt-2"
              placeholder="비밀번호"
              v-model="password"
            />
          </div>
          <div class="modal-footer">
            <button @click="login" type="button" class="btn btn-primary">
              로그인
            </button>
            <button
              @click="showModal = false"
              type="button"
              class="btn btn-secondary"
            >
              닫기
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import jwtDecode from "jwt-decode";

export default {
  name: "AppNavbar",

  data() {
    return {
      showModal: false,
      email: "",
      password: "",
      loggedIn: !!localStorage.getItem("accessToken"),
      isBookmarkView: false,
    };
  },

  created() {
    this.checkTokenExpiration();
  },

  methods: {
    login() {
      const loginData = {
        email: this.email,
        password: this.password,
      };

      axios
        .post("http://localhost:9000/users/login", loginData)
        .then((response) => {
          const accessToken = response.data.accessToken;
          const refreshToken = response.data.refreshToken;

          localStorage.setItem("accessToken", accessToken);
          localStorage.setItem("refreshToken", refreshToken);

          this.loggedIn = true;
          this.showModal = false;

          const token = localStorage.getItem("accessToken");
          const decodedToken = jwtDecode(token);

          localStorage.setItem("userId", decodedToken.sub);
          localStorage.setItem("email", decodedToken.email);
        })
        .catch((error) => {
          console.error("로그인에 실패했습니다:", error);
          alert("로그인에 실패했습니다.");
        });
    },

    logout() {
      localStorage.removeItem("accessToken");
      localStorage.removeItem("refreshToken");
      localStorage.removeItem("userId");
      localStorage.removeItem("email");

      this.loggedIn = false;
    },

    checkTokenExpiration() {
      const token = localStorage.getItem("accessToken");
      if (token) {
        const decodedToken = jwtDecode(token);
        const currentTime = Date.now() / 1000;

        if (decodedToken.exp < currentTime) {
          this.refreshAccessToken();
        }
      }
    },

    refreshAccessToken() {
      const refreshToken = localStorage.getItem("refreshToken");

      if (!refreshToken) {
        this.logout();
        return;
      }

      axios
        .post("http://localhost:9000/users/refresh", {
          refreshToken: refreshToken,
        })
        .then((response) => {
          const newAccessToken = response.data.accessToken;
          localStorage.setItem("accessToken", newAccessToken);
        })
        .catch((error) => {
          console.error(
            "리프레시 토큰으로 엑세스 토큰을 발급하는데 실패했습니다:",
            error
          );
          this.logout();
        });
    },

    toggleView() {
      this.isBookmarkView = !this.isBookmarkView;

      if (this.isBookmarkView) {
        this.$emit("bookmarkClicked");
      } else {
        this.$emit("showAllCenters");
      }
    },
  },
};
</script>

<style scoped>
/* 네비게이션 바 설정 */
.custom-navbar {
  background-color: white;
}
.custom-navbar .navbar-brand,
.custom-navbar .nav-link {
  color: black;
}

/* 모달의 배경 투명도 설정 */
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1040;
}
</style>
