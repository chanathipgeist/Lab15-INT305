<script setup>
import { ref, onMounted, watch } from 'vue'
import { useRoute } from "vue-router"
import { collection, query, where, getDocs, onSnapshot } from "firebase/firestore"
import db from "../firebase/init.js"
import PostItem from "../components/PostItem.vue"

const user = ref("")
const posts = ref([])
const route = useRoute() 

async function getPosts(){
  posts.value = []
  user.value = route.params.user
  const postRef = collection(db, "posts");
  const qry = query(postRef, where("user","==",user.value));
  console.log(qry)
}

watch( () => route.params.user, getPosts)

onMounted(() => {
  getPosts() 
  console.log(user.value)
})

</script>

<template>
    <h3>Posts : {{user}}</h3>
    <PostItem v-for="post in posts" :post="post" :key="post.id" />
</template>

<style scoped>

</style>

