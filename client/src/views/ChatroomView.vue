<script>
import io from "socket.io-client";
import { mapActions, mapState, mapWritableState } from "pinia";
import { useIndexStore } from "../stores";
export default {
  name: "ChatroomView",
  data() {
    return {
      activeUser: localStorage.getItem("name"),
      text: "",
      messages: [],
      joinedChatroom: false,
      baseUrl: "http://localhost:3000",
    };
  },
  methods: {
    // ...mapActions(useIndexStore, ['joinChatroom']),
    joinChatroom() {
      // console.log('wadidaw')
      this.joinedChatroom = true;
      this.socketInstance = io(this.baseUrl);
      this.socketInstance.on("message:received", (data) => {
        this.messages = this.messages.concat(data);
      });
    },
    addMessage() {
      const message = {
        id: new Date().getTime(),
        text: this.text,
        user: this.activeUser,
      };
      this.messages = this.messages.concat(message);
      this.socketInstance.emit("message", message);
    },
    sendChat() {
      this.addMessage();
      this.text = "";
    },
  },
  computed: {
    // ...mapState(useIndexStore, ['joinedChatroom'])
  },
};
</script>

<template>
  <div class="container justify-content-center d-flex pt-4 mt-4">
    <div class="parent-container">
      <div class="name-container">
        <!-- <input type='text' class="user-name" v-model="currentUser" /> -->
        <button @click.prevent="joinChatroom" class="join-button" v-if="joinedChatroom === false">Join Chat Room</button>
      </div>
    </div>
    <br />

    <div v-if="joinedChatroom" class="text-input-container">
      <div class="list-container">
        <div v-for="msg in messages" :key="msg.id" style="color:gold;">
          <b>
            {{ msg.user }}
          </b>
          :{{ msg.text }}
        </div>
      </div>
      <div>
        <textarea
          v-model="text"
          class="text-messages"
          style="height:75px;width:350px"
          @keyup.enter="sendChat"
        ></textarea>
      </div>
    </div>
  </div>
</template>
