<script setup>
import axios from "axios";
import { reactive, onMounted, onUpdated } from "vue";

let state = reactive({
  posts: [],
  loading: true,
  pages: 0,
});

async function fetchPosts(n, p) {
  try {
    state.loading = true;
    const response = await axios
      .get(`https://jsonplaceholder.typicode.com/posts?_page=${p}&_limit=${n}`)
      .then((res) => res);

    const data = await response.data;
    state.posts = state.posts.concat(data);
    state.loading = false;
  } catch (err) {
    console.log(err);
  }
}
function handleIntersecting(entries) {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      setTimeout(() => {
        fetchPosts(10, state.pages);
      }, 500);
      state.pages = state.pages + 1;
    }
  });
}
onMounted(() => {
  console.log(state.loading);
  const options = {
    root: null,
    rootMargin: "0px",
    threshold: 0.25,
  };
  const observer = new IntersectionObserver(handleIntersecting, options);
  const observerHtmlElement = document.getElementById("observer");
  observer.observe(observerHtmlElement);
});

onUpdated(() => {
  console.log(state.pages);
});
</script>

<template>
  <div class="nav-info">
    <p>posts loaded {{ state.posts.length }}</p>
  </div>
  <div class="posts-container">
    <div class="post" v-for="post in state.posts" :key="post.id">
      <img src="https://picsum.photos/400/400" alt="img" />
      <p>{{ post.title }}</p>
    </div>
    <div v-if="state.loading" class="loading">
      <button class="btn btn-primary" type="button" disabled>
        <span
          class="spinner-grow spinner-grow-sm"
          role="status"
          aria-hidden="true"
        ></span>
        Loading...
      </button>
    </div>
  </div>
</template>

<style scoped>
.posts-container {
  width: 70vw;
  margin: 10px auto;
  display: grid;
  grid-template-columns: 1fr 1fr;
}
.post {
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px,
    rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
  width: 400px;
  margin: 10px auto;
  background: #777;
  color: #fff;
}

.post p {
  font-size: 18px;
  text-align: center;
  padding: 10px;
}

.loading {
  position: fixed;
  bottom: 0;
  right: 0;
  left: 0;
  display: grid;
  place-content: center;
  color: #fff;
  z-index: 9999;
}
.nav-info {
  position: fixed;
  right: 0;
  top: 40px;
  padding: 10px;
  background: #007aff;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 5px 0 0 5px;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px,
    rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
  color: #fff;
}
.nav-info p {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 0;
}
</style>
