<template>
    <div class="chat-container">
        <h1>Streaming Chat Test</h1>
        <div v-if="messages.length === 0">
            Pas de messages pour l'instant...
        </div>
        <div v-for="(msg, index) in messages" :key="index" class="message">
            {{ msg.content }} <span v-if="msg.isComplete">(Terminé)</span>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import Echo from "laravel-echo";
import Pusher from "pusher-js";

window.Pusher = Pusher;

const messages = ref([]);

onMounted(() => {
    console.log("ChatWindow monté");
    const channelName = "chat.1";
    console.log("Tentative d'abonnement au canal :", channelName);

    window.Echo.private(channelName)
        .subscribed(() => {
            console.log("✅ Abonné au canal", channelName);
        })
        .error((error) => {
            console.error(
                "❌ Erreur lors de l'abonnement au canal",
                channelName,
                error
            );
        })
        .listen(".message.streamed", (data) => {
            console.log("Message streamé reçu:", data);
            // Ajoute ou met à jour les messages au fur et à mesure
            messages.value.push(data);
        });
});
</script>

<style>
.chat-container {
    padding: 20px;
    background: #222;
    color: #fff;
    min-height: 100vh;
}
.message {
    padding: 10px;
    border-bottom: 1px solid #444;
}
</style>
