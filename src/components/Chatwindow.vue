<template>
    <q-card flat bordered class="window">
      <q-card-section>
        <div class="text-h6">Support Chat</div>
      </q-card-section>

      <q-separator inset />

      <q-card-section class="messages">
        <q-chat-message v-for="message in messages"
                        :key="message.id"
                        :name="message.name"
                        :text=[message.message]
                        :stamp="message.date"
                        :sent="message.name === 'Admin'"
        />
      </q-card-section>

      <q-card-section>
        <q-input
          v-model="message"
          filled
          label="Type your message here"
          @keyup.enter="sendMessage"
        />
      </q-card-section>
    </q-card>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getFirestore, collection, setDoc, onSnapshot, doc} from 'firebase/firestore';

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
let messageCollection;

export default {
  name: "Chatwindow",
  data() {
    return {
      message: "",
      messages: [],
      user: "alex",
      userRef: "Admin",
      users: [],
    }
  },
  methods: {
    async sendMessage() {
      if (this.message === "") {
        return;
      }
      let date = new Date();
      date = date.toLocaleTimeString('en-GB', { hour: '2-digit', minute: '2-digit', second: '2-digit' });

      const message = {
        message: this.message,
        name: this.userRef,
        date: date
      }

      messageCollection = collection(db, 'messages-' + this.user);
      await setDoc(doc(messageCollection, this.user + "-" + Date.now()), message);
      this.message = "";
    }
  },
  mounted() {
    let audio = new Audio("/notification.mp3");
    messageCollection = collection(db, 'messages-' + this.user);
    onSnapshot(messageCollection, (querySnapshot) => {
      this.messages = [];
      querySnapshot.forEach((doc) => {
        this.messages.push(doc.data());
      });
      // If a new message is added, scroll to the bottom of the chat
      this.$nextTick(() => {
        let messages = document.querySelector(".messages");
        messages.scrollTop = messages.scrollHeight;
        if ((this.messages[this.messages.length - 1].name !== this.userRef
          || this.messages[this.messages.length - 1].name !== "Admin") && document.visibilityState === "hidden") {
          audio.play();
        }
      });
    });

  }
}
</script>

<style scoped lang="scss">
.window {
  z-index: 100;
  position: absolute;
  display: flex;
  flex-direction: column;
  align-items: center;
  background: #f5f5f5;
  border: 1px solid black;
  width: 300px;
  height: 450px;
  bottom: 25px;
  right: 50px;

  .window-title{
    background: #478aef;
    width: 100%;
    margin: 0;
    text-align: center;
    top: 0;
    position: relative;
    padding: 5px;
    border-bottom: 1px solid black;
  }

  .messages {
    height: auto;
    overflow: scroll;
  }
}
</style>
