<template>
  <div class="chat">
    <div class="chat-conversation-father" v-if="user.name">
      <chat-conversation :socket="socket"/>
      <input @keyup="onKeyUp" class="chat-input" v-model="message" />
      <button class="chat-button" @click="sendMessage">Enviar</button>
    </div>
    <div class="chat-error" v-else>
      Erro Inesperado ao inserir um usuário 
    </div>
  </div>
</template>

<script>
import io from 'socket.io-client';
import ChatConversation from './components/ChatConversation.vue';

export default {
  name: 'app',
  components: {
    ChatConversation,
  },
  data() {
    return {
      message: '',
      socket: null,
      user: {
        name: null
      }
    };
  },
  created() {
    this.user.name = prompt('Please enter your username:', '');

    if (this.user.name) {
      
      this.socket = io('https://pr-vonix-chat-server.herokuapp.com/');
      
      this.socket.on('connect', () => {
        this.connect();
      });
    }
  },
  methods: {
    connect() {
      this.socket.emit('connected', this.user);
    },
    sendMessage() {
      this.socket.emit('message', {
        user: this.user,
        message: this.message
      });
      this.message = '';
    },
    onKeyUp(event) {
      if (event.keyCode === 13) {
        this.sendMessage();
      }
    }
  },
  beforeDestroy() {
    if (this.socket) {
      this.socket.emit('disconnected', {
        user: this.user
      });
    }
  },
}
</script>

<style scope>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300&display=swap');

html, body {
  height: 100%;
  font-family: 'Roboto', sans-serif;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.chat {
  height: 100%;
  padding-top: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.chat-conversation-father {
  height: 500px;
  width: 800px;
  flex: 4;
  display: flex;
  flex-direction: column;
}
.chat-input {
  width: 600px;
  height: 50px;
  padding: 0 20px;
  font-size: 14px;
  border: 1px solid #ddd;
}
.chat-button {
  width: 600px;
  height: 50px;
  margin-top: 10px;
  font-size: 14px;
  text-align: center;
  line-height: 50px;
  font-weight: bold;
  background: #222a68;
  color: #FFF;
}
.chat-error {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
</style>