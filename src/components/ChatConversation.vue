<template>
  <div class="chat-conversation">
    <div
      v-for="message in messages"
      :message="message"
      v-bind:key="message.id"
    >
      <strong>{{ message.user.name }}: </strong>
      {{ message.message }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'ChatConversation',
  props: {
    socket: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      messages: []
    };
  },
  mounted() {
    this.socket.on('message', (data) => {
      this.messages.push(data);
    });
    this.socket.on('disconnect', () => {
      this.messages = [];
    });
  }
}
</script>

<style scope>
.chat-conversation {
  width: 600px;
  height: 400px;
  margin: 10px 0;
  padding: 10px;
  border: 1px solid #ddd;
}
</style>
