<script setup>
import axios from "axios";
import { reactive, onMounted, onUpdated } from "vue";

let state = reactive({
  posts: [],
  loading: true,
});

async function fetchPosts(n) {
  try {
    state.loading = true;
    const response = await axios
      .get(`https://jsonplaceholder.typicode.com/posts?_page=1&_limit=${n}`)
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
        fetchPosts(10);
      }, 2000);
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
  console.log(state.loading);
});
</script>

<template>
  <div class="posts-container">
    <div class="post" v-for="post in state.posts" :key="post.id">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
    <div v-if="state.loading" class="loading">
      <h1>Loading....</h1>
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
  background: rgb(24, 146, 24);
  color: #fff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px,
    rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
  text-align: center;
  margin: 10px;
}

.post h2 {
  font-size: 20px;
  color: #333;
}

.post p {
  font-size: 18px;
}

.loading {
  background: #000;
  height: 100vh;
  width: 100vw;
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  display: grid;
  place-content: center;
  color: #fff;
  opacity: 0.8;
}
</style>
