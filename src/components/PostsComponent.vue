<script setup>
import axios from "axios";
import { reactive, onMounted } from "vue";

let state = reactive({
  posts: [],
});

async function fetchPosts(n) {
  try {
    const response = await axios
      .get(`https://jsonplaceholder.typicode.com/posts?_page=1&_limit=${n}`)
      .then((res) => res);
    const data = await response.data;
    state.posts = state.posts.concat(data);
  } catch (err) {
    console.log(err);
  }
}
function handleIntersecting(entries) {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      fetchPosts(10);
      console.log(state.posts);
    }
  });
}
onMounted(() => {
  fetchPosts(10);
  const options = {
    root: null,
    rootMargin: "0px",
    threshold: 0.5,
  };
  const observer = new IntersectionObserver(handleIntersecting, options);
  const observerHtmlElement = document.getElementById("observer");
  observer.observe(observerHtmlElement);
});
</script>

<template>
  <div class="posts-container">
    <div class="post" v-for="post in state.posts" :key="post.id">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
  </div>
</template>

<style scoped>
.posts-container {
  width: 70vw;
  margin: 10px auto;
}
.post {
  background: rgb(24, 146, 24);
  color: #fff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px,
    rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
  text-align: center;
  margin-bottom: 10px;
}

.post h2 {
  font-size: 20px;
  color: #333;
}

.post p {
  font-size: 18px;
}
</style>
