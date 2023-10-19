<template>
  <div id="centerList" class="col-3">
    <ul>
      <!-- 데이터를 props로 받아 사용 -->
      <li v-for="center in centers" :key="center.id">
        <h5 class="center-content">{{ center.name }}</h5>
        <p class="center-content">주소: {{ center.address }}</p>
        <p class="center-content">연락처: {{ center.tel }}</p>
        <a class="center-content" :href="center.homepage">{{
          center.homepage
        }}</a>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    centers: Array, // centers 데이터를 props로 받음
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
}

li:first-child {
  margin-top: 0;
}

li:last-child {
  margin-bottom: 0;
}
</style>
