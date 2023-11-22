<template>
  <div class="pony-post-management">
    <h1 class="pony-title">🌈 Ponyville Magic 🌈</h1>

    <div class="pony-post-form">
      <h2 class="pony-subtitle">Create/Edit Your Magic Post</h2>
      <form @submit.prevent="savePost">
        <label class="pony-label" for="title">Magical Title:</label>
        <input class="pony-input" type="text" v-model="newPost.title" required>

        <label class="pony-label" for="description">Magical Description:</label>
        <textarea class="pony-input" v-model="newPost.description" required></textarea>

        <label class="pony-label" for="author">Magical Author:</label>
        <input class="pony-input" type="text" v-model="newPost.author" required>

        <button class="pony-button" type="submit">✨ Save Magic Post ✨</button>
      </form>
    </div>

    <div class="pony-all-posts">
      <h2 class="pony-subtitle">📚 All Magical Posts 📚</h2>
      <ul>
        <li v-for="(post, index) in posts" :key="index" class="pony-post-item">
          <h3 class="pony-post-title">{{ post.title }}</h3>
          <p class="pony-post-description">{{ post.description }}</p>
          <span class="pony-post-author">🦄 Magical Author: {{ post.author }}</span>
          <div class="pony-post-buttons">
            <button @click="editPost(index)" class="pony-edit-button">✏️ Edit Magic</button>
            <button @click="removePost(index)" class="pony-remove-button">🗑️ Remove Magic</button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const posts = ref([]);
const newPost = ref({ title: '', description: '', author: '' });
let editingIndex = ref(null);

const fetchPosts = async () => {
  try {
    const response = await axios.get('http://localhost:8080/posts');
    posts.value = response.data;
  } catch (error) {
    console.error(error);
  }
};

const savePost = async () => {
  try {
    if (editingIndex.value !== null) {
      const response = await axios.put(`http://localhost:8080/posts/${posts.value[editingIndex.value].id}`, newPost.value);
      posts.value[editingIndex.value] = response.data;
      editingIndex.value = null;
    } else {
      const response = await axios.post('http://localhost:8080/posts', newPost.value);
      posts.value.push(response.data);
    }

    newPost.value = { title: '', description: '', author: '' };
  } catch (error) {
    console.error(error);
  }
};

const editPost = (index) => {
  newPost.value = { ...posts.value[index] };
  editingIndex.value = index;
};

const removePost = async (index) => {
  try {
    const response = await axios.delete(`http://localhost:8080/posts/${posts.value[index].id}`);
    if (response.status === 200) {
      posts.value.splice(index, 1);
    } else {
      console.error('Failed to delete post');
    }
  } catch (error) {
    console.error(error);
  }
};

onMounted(fetchPosts);
</script>

<style>
body {
  background: linear-gradient(180deg, #ffe6ff, #cc99ff);
  color: #4d004d;
  font-family: 'Comic Sans MS', cursive, sans-serif;
}

.pony-title {
  color: #9900cc;
  font-size: 60px;
  text-align: center;
  margin-bottom: 20px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

.pony-post-management {
  max-width: 800px;
  margin: auto;
  transition: transform 0.3s ease-in-out;
}

.pony-post-management:hover {
  transform: scale(1.01);
}

.pony-post-form, .pony-all-posts {
  background-color: #ffd9ff;
  padding: 20px;
  margin-bottom: 20px;
  border: 1px solid #ffccff;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
}

.pony-subtitle {
  color: #9900cc;
  font-size: 24px;
  margin-bottom: 10px;
}

.pony-all-posts ul {
  list-style: none;
  padding: 0;
}

.pony-label {
  display: block;
  margin-bottom: 5px;
  color: #9900cc;
}

.pony-input {
  width: 100%;
  padding: 8px;
  margin-bottom: 10px;
  box-sizing: border-box;
  background-color: #ffe6ff;
  color: #4d004d;
  border: 1px solid #ffccff;
  border-radius: 5px;
}

.pony-button {
  background-color: #ff66b2;
  color: #4d004d;
  padding: 10px 15px;
  border: none;
  cursor: pointer;
  border-radius: 5px;
}

.pony-button:hover {
  background-color: #ff3385;
}

.pony-all-posts {
  text-align: center;
}

.pony-post-buttons button {
  font-size: 16px;
  padding: 12px 20px;
}

.pony-post-buttons .pony-edit-button {
  background-color: #ffcc00;
  color: #4d004d;
}

.pony-post-buttons .pony-remove-button {
  background-color: #ff3300;
  color: #4d004d;
}
</style>