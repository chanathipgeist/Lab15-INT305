<script setup>
import { RouterLink, RouterView, } from 'vue-router'
import { ref, onMounted } from 'vue'
import { query, collection, getDocs } from 'firebase/firestore'
import db from './firebase/init.js'
import UserList from './components/UserList.vue'

const users = ref([])
const posts = ref([])
async function getUsers(){
  const userRef = collection(db, "users");
    const qry = query(userRef);
   
    const querySnap = await getDocs(qry);
    querySnap.forEach((doc) => {
    let data = doc.data();
    data.id = doc.id;
    users.value.push(data);
})
}

async function getPosts(){
  const postRef = collection(db, "posts");
    const qry = query(postRef);
   
    const querySnap = await getDocs(qry);
    querySnap.forEach((doc) => {
    let data = doc.data();
    data.id = doc.id;
    posts.value.push(data);
})
}
onMounted(() => {
    getUsers() 
    getPosts()
    console.log(users.value)
    console.log(posts.value)

})
</script>

<template>
  <div>
    <div>
      <UserList :users=users />
    </div>
  </div>
  <div class="content">
    <RouterView />
  </div>
</template>

<style scoped>

</style>

