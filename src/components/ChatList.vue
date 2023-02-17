<template>
<q-card>
  <q-card-section>
    <q-list>
      <q-item v-for="chat in chatList" :key="chat.id" clickable @click="goToChat(chat.username)">
        <q-item-section avatar>
          <q-avatar>
            <img src="https://cdn.quasar.dev/img/avatar.png" alt="asd">
          </q-avatar>
        </q-item-section>
        <q-item-section>
          <q-item-label>{{chat.username}}</q-item-label>
          <q-item-label caption>{{chat.lastMessage}}</q-item-label>
        </q-item-section>
        <q-item-section side>
          <q-item-label caption>{{chat.lastMessageDate}}</q-item-label>
        </q-item-section>
      </q-item>
    </q-list>
  </q-card-section>
</q-card>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getFirestore, collection, addDoc, doc, getDocs, setDoc, collectionGroup } from 'firebase/firestore';
import axios from "axios";

const firebaseConfig = {
  apiKey: "AIzaSyDI3S-pmy8cjQgd9C25-BhC7nq7m-AIzMY",
  authDomain: "isphero.firebaseapp.com",
  databaseURL: "https://isphero-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "isphero",
  storageBucket: "isphero.appspot.com",
  messagingSenderId: "911722930693",
  appId: "1:911722930693:web:ec66e3f3d8f6df1f41726c",
  measurementId: "G-S2C6VQWGXP"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app)
export default {
  name: "ChatList",
  data() {
    return {
      chatList: [],
      users: [],
    }
  },
  methods: {
    getListOfChats() {
      const messageCollectionRef = collection(db, "messages");
      getDocs(messageCollectionRef).then((querySnapshot) => {
        querySnapshot.forEach((doc) => {
          const data = doc.data();
          const chat = {
            id: doc.id,
            username: data.username,
            lastMessage: data.lastMessage,
            lastMessageDate: data.lastMessageDate,
          }
          this.chatList.push(chat);
          this.users.push(data.username)
        });
      });
      this.$emit("userList", this.users);
    },
    goToChat(username){
      this.$emit("chat", username);
    }
  },
  async mounted() {
    this.getListOfChats();
  }
}
</script>

<style scoped>

</style>
