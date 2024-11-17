<template>
  <div class="chatbot">
    <div class="chat-box">
      <div v-for="(message, index) in messages" :key="index" class="message">
        {{ message }}
      </div>
    </div>
    <div class="input-box">
      <input
        type="text"
        v-model="newMessage"
        @keyup.enter="sendMessage"
        placeholder="Type a message..."
      />
      <input
        type="file"
        id="fileUpload"
        @change="uploadFile"
      />
      <label for="fileUpload" class="file-upload-label">📎</label>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      messages: [],
      newMessage: ''
    };
  },
  methods: {
    sendMessage() {
      if (this.newMessage.trim() !== '') {
        // Benutzer-Nachricht wird angezeigt
        this.messages.push(`You: ${this.newMessage}`);

        // Anfrage an das Backend
        axios.get('http://localhost:8080/message', {
          params: { message: this.newMessage }  // Wir senden die Nachricht als Parameter
        })
        .then(response => {
          // Antwort vom Server im Chat anzeigen
          this.messages.push(`Server: ${response.data}`);
        })
        .catch(error => {
          console.error("Es gab ein Problem mit der Anfrage:", error);
          this.messages.push("Fehler beim Senden der Nachricht.");
        });

        // Eingabefeld leeren
        this.newMessage = '';
      }
    },
    uploadFile(event) {
      const file = event.target.files[0];
      if (file) {
        this.messages.push(`Uploaded file: ${file.name}`);
      }
    }
  }
};
</script>

<style scoped>
.chatbot {
  max-width: 600px;
  margin: 50px auto;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #121212;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

.chat-box {
  border: 1px solid #00ff00;
  background-color: #1e1e1e;
  padding: 10px;
  height: 400px;
  overflow-y: auto;
  color: #00ff00;
  border-radius: 8px 8px 0 0;
}

.message {
  margin-bottom: 10px;
  padding: 8px;
  background-color: #2a2a2a;
  border-radius: 5px;
  max-width: 70%;
}

.input-box {
  display: flex;
  padding: 10px;
  background-color: #2a2a2a;
  border-radius: 0 0 8px 8px;
  box-shadow: 0 -2px 4px rgba(0, 0, 0, 0.2);
}

.input-box input[type="text"] {
  flex: 1;
  padding: 10px;
  border: 1px solid #00ff00;
  background-color: #121212;
  color: #00ff00;
  outline: none;
  border-radius: 5px;
  margin-right: 10px;
  transition: border-color 0.3s;
}

.input-box input[type="text"]:focus {
  border-color: #00cc00;
}

.input-box input[type="file"] {
  display: none;
}

.file-upload-label {
  padding: 10px;
  background-color: #00ff00;
  color: #121212;
  cursor: pointer;
  border-radius: 5px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.3s, transform 0.2s;
}

.file-upload-label:hover {
  background-color: #00cc00;
  transform: scale(1.05);
}
</style>