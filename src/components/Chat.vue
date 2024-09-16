<template>
  <div class="flex flex-col h-screen bg-gray-100 p-6">
    <!-- Chat Window -->
    <div class="flex-1 overflow-y-auto">
      <div v-for="message in messages" :key="message.id" class="mb-4">
        <div v-if="message.from === 'user'" class="text-right">
          <div class="inline-block bg-blue-500 text-white p-3 rounded-lg">
            {{ message.text }}
          </div>
        </div>
        <div v-else class="text-left">
          <div class="inline-block bg-gray-300 text-black p-3 rounded-lg">
            <!-- Mensaje de texto del bot -->
            <p>{{ message.text }}</p>
            <!-- Si el mensaje incluye un GIF, lo mostramos -->
            <img v-if="message.gif" :src="message.gif" alt="YesNo GIF" class="mt-2 w-32 h-32 rounded-lg" />
          </div>
        </div>
      </div>
    </div>

    <!-- Input Field -->
    <div class="mt-4">
      <input 
        v-model="newMessage"
        @keyup.enter="sendMessage"
        type="text" 
        placeholder="Escribe un mensaje..." 
        class="w-full p-3 border rounded-lg focus:outline-none focus:border-blue-500"
      />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newMessage: '',
      messages: [
        { id: 1, text: 'Hola, ¿cómo estás?', from: 'bot' },
        { id: 2, text: '¡Hola! Estoy bien, gracias. ¿Y tú?', from: 'user' },
      ],
    };
  },
  methods: {
    async sendMessage() {
      if (this.newMessage.trim() === '') return;

      // Agregar el mensaje del usuario al chat
      this.messages.push({
        id: this.messages.length + 1,
        text: this.newMessage,
        from: 'user',
      });

      // Limpiar el campo de entrada
      this.newMessage = '';

      // Simular una pequeña pausa antes de que el bot responda
      setTimeout(() => {
        this.botReply();
      }, 1000);
    },
    
    async botReply() {
      try {
        // Hacer la petición a la API de YesNo
        const response = await fetch('https://yesno.wtf/api');
        const data = await response.json();

        // Agregar la respuesta del bot con el texto y el GIF
        this.messages.push({
          id: this.messages.length + 1,
          text: data.answer === 'yes' ? 'Sí' : 'No', // Respuesta "Sí" o "No"
          gif: data.image, // GIF asociado
          from: 'bot',
        });
      } catch (error) {
        // Manejo de errores en caso de fallo en la API
        this.messages.push({
          id: this.messages.length + 1,
          text: 'Error al obtener la respuesta del bot.',
          from: 'bot',
        });
      }
    },
  },
};
</script>

<style scoped>
/* Puedes agregar estilos adicionales si lo deseas */
</style>
