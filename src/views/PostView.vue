<script setup>
import { ref, onMounted, watch } from 'vue'
import { useRoute } from "vue-router"
import { collection, query, where, getDocs, onSnapshot } from "firebase/firestore"
import db from "../firebase/init.js"
import PostItem from "../components/PostItem.vue"
const user = ref("")
const posts = ref([])
const route = useRoute()
const count = ref(0)
async function getPosts() {
  posts.value = []
  user.value = route.params.user
  const postRef = collection(db, "posts");
  const qry = query(postRef, where("user", "==", user.value));
  const unsubscribe = onSnapshot(qry, (querySnap) => {
    posts.value = []
    count.value = 0      
    console.log(querySnap)
    count.value = querySnap.size
    querySnap.forEach(async (doc) => {
       let data = doc.data();
      data.id = doc.id;
      const commentRef = collection(db, "posts", doc.id, "comments");
      const queryComment = await getDocs(query(commentRef));
      console.log(queryComment)
      
      data.comments = []
      queryComment.forEach((com) => {
        let comment = com.data();
        comment.id = com.id;
        console.log(comment)
        data.comments.push(comment);
      })
      posts.value.push(data);
    });
  })
}

watch( () => route.params.user, getPosts)

onMounted(() => {
  getPosts() 
  console.log(user.value)
  console.log(posts.value)
})

</script>

<template>
  <h3>Posts : {{ user }} </h3>
    <PostItem v-for="post in posts" :post="post" :key="post.id" />
</template>

<style scoped>

</style>

