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
          <button @click="showModal = true" class="btn btn-primary ml-auto">
            Login
          </button>
        </div>
      </div>
    </nav>

    <!-- 로그인 모달창 -->
    <div v-if="showModal" class="modal show d-block">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Login</h5>
            <button @click="showModal = false" type="button" class="close">
              <span>&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <input
              type="text"
              class="form-control"
              placeholder="Email"
              v-model="email"
            />
            <input
              type="password"
              class="form-control mt-2"
              placeholder="Password"
              v-model="password"
            />
          </div>
          <div class="modal-footer">
            <button @click="login" type="button" class="btn btn-primary">
              Login
            </button>
            <button
              @click="showModal = false"
              type="button"
              class="btn btn-secondary"
            >
              Close
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AppNavbar",
  data() {
    return {
      showModal: false,
      email: "",
      password: "",
    };
  },
  methods: {
    login() {
      console.log(this.email, this.password);
      this.showModal = false;
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
