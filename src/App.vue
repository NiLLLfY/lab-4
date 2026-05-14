<template>
  <div class="container">
    <CustomSelect v-model="selectedUserId" :options="userOptions" :disabled="isLoadingUsers">
      <template #label>
        <strong class="label-text">1. Выберите пользователя (User):</strong>
      </template>
    </CustomSelect>

    <CustomSelect
      v-if="selectedUserId !== ''"
      v-model="selectedPostId"
      :options="postOptions"
      :disabled="isLoadingPosts"
    >
      <template #label>
        <strong class="label-text">2. Выберите пост (Post):</strong>
        <span v-if="isLoadingPosts" class="loader">...Загрузка постов</span>
      </template>
    </CustomSelect>

    <CustomSelect
      v-if="selectedPostId !== ''"
      v-model="selectedCommentId"
      :options="commentOptions"
      :disabled="isLoadingComments"
    >
      <template #label>
        <strong class="label-text">3. Выберите комментарий (Comment):</strong>
        <span v-if="isLoadingComments" class="loader">...Загрузка веток</span>
      </template>
    </CustomSelect>

    <div v-if="selectedCommentData" class="result">
      <p><strong>Email:</strong> {{ selectedCommentData.email }}</p>
      <p><strong>Текст:</strong> {{ selectedCommentData.body }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import CustomSelect, { type SelectOption } from './components/CustomSelect.vue'

interface User {
  id: number
  name: string
}
interface Post {
  id: number
  title: string
}
interface Comment {
  id: number
  name: string
  email: string
  body: string
}

const users = ref<User[]>([])
const posts = ref<Post[]>([])
const comments = ref<Comment[]>([])

const selectedUserId = ref<number | ''>('')
const selectedPostId = ref<number | ''>('')
const selectedCommentId = ref<number | ''>('')

const isLoadingUsers = ref(false)
const isLoadingPosts = ref(false)
const isLoadingComments = ref(false)

const userOptions = computed<SelectOption[]>(() =>
  users.value.map((u) => ({ value: u.id, label: u.name })),
)
const postOptions = computed<SelectOption[]>(() =>
  posts.value.map((p) => ({ value: p.id, label: p.title })),
)
const commentOptions = computed<SelectOption[]>(() =>
  comments.value.map((c) => ({ value: c.id, label: c.name })),
)

const selectedCommentData = computed(() =>
  comments.value.find((c) => c.id === selectedCommentId.value),
)

onMounted(async () => {
  isLoadingUsers.value = true
  try {
    const res = await fetch('https://jsonplaceholder.typicode.com/users')
    users.value = await res.json()
  } finally {
    isLoadingUsers.value = false
  }
})

watch(selectedUserId, async (newUserId) => {
  selectedPostId.value = ''
  selectedCommentId.value = ''
  posts.value = []
  comments.value = []

  if (newUserId !== '') {
    isLoadingPosts.value = true
    try {
      const res = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${newUserId}`)
      posts.value = await res.json()
    } finally {
      isLoadingPosts.value = false
    }
  }
})

watch(selectedPostId, async (newPostId) => {
  selectedCommentId.value = ''
  comments.value = []

  if (newPostId !== '') {
    isLoadingComments.value = true
    try {
      const res = await fetch(`https://jsonplaceholder.typicode.com/comments?postId=${newPostId}`)
      comments.value = await res.json()
    } finally {
      isLoadingComments.value = false
    }
  }
})
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 2rem auto;
  font-family: Arial, sans-serif;
  padding: 1rem;
}
.label-text {
  color: #2c3e50;
}
.loader {
  font-size: 0.8rem;
  color: #e67e22;
  margin-left: 10px;
}
.result {
  margin-top: 2rem;
  padding: 1.5rem;
  border: 1px solid #ddd;
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  color: #333333;
}
.result p {
  margin: 0.5rem 0;
  line-height: 1.6;
}
.result strong {
  color: #000000;
}
</style>
