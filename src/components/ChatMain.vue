<template>
  <div class="chat">
    <div class="flex-1" dir="rtl">
      <!-- Chat Messages -->
      <div class="h-screen overflow-y-auto p-4 pb-36">
          <!-- Incoming Message -->
          <div v-for="(message, index) in messages" :key="index" class="flex mb-4 cursor-pointer" :class="{'justify-end': message.type === 'received'}">
            <div class="flex flex-col max-w-96 bg-pink-500 text-white rounded-lg p-3 gap-3" :class="{'!bg-indigo-500': message.type === 'sent'}">
              <p class="text-start">{{message.text}}</p>
              <div class="flex gap-2 items-center text-sm text-start">
                <strong>{{ message.type === 'received' ? 'ارسال در زمان' : 'دریافت در زمان' }}</strong> 

                <span>{{ message.time }}</span>
              </div>
            </div>
          </div>
      </div>
      
      <!-- Chat Input -->
      <footer class="bg-gray-100 rounded-lg p-4 absolute bottom-0 w-full" dir="rtl">
          <div class="flex items-center">
              <input v-model="newMessage" @keyup.enter="sendMessage" type="text" placeholder="پیام خود را بنویسید" class="w-full p-2 rounded-md border border-gray-400 focus:outline-none focus:border-blue-500">
              <button @click="sendMessage" class="bg-indigo-500 text-white px-4 py-2 rounded-md ms-2">ارسال</button>
          </div>
      </footer>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      socket: null,
      messages: [],
      newMessage: ''
    };
  },
  created() {
    this.connectWebSocket();
  },
  methods: {
    connectWebSocket() {
      this.socket = new WebSocket('wss://echo.websocket.org');
      
      this.socket.onmessage = (event) => {
        this.messages.push({
          text: event.data,
          time: new Date().toLocaleTimeString(),
          type: 'received'
        });
      };

      this.socket.onopen = () => {
        console.log('Connected to WebSocket');
      };

      this.socket.onclose = () => {
        console.log('Disconnected from WebSocket');
      };
    },
    sendMessage() {
      if (this.newMessage.trim() !== '') {
        const message = this.newMessage;
        this.socket.send(message);
        this.messages.push({
          text: message,
          time: new Date().toLocaleTimeString(),
          type: 'sent'
        });
        this.newMessage = '';
      }
    }
  }
};
</script>

<style>
</style>
