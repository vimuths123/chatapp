<template>
    <div v-if="!username">
        <input type="text" v-model="tempUsername" placeholder="Enter your username">
        <button @click="setUsername">Join Chat</button>
    </div>
    <div class="chat-box" v-else>
        <div class="chat-box-header">Chat</div>
        <div class="chat-box-messages" id="chat-messages">
            <!-- <div class="message other-user">Hi, how are you?</div>
            <div class="message current-user">I'm good, thanks! And you?</div> -->
            <div v-for="message in messages" :key="message.message_text" 
                :class="message.user == username ? 'message current-user' : 'message other-user'">
                {{ message.message_text }}
            </div>

        </div>
        <div class="chat-box-input">
            <input type="text" v-model="newMessage" class="input_border" placeholder="Type a message..." />
            <button type="button" @click="setMessage">Send</button>
        </div>
    </div>
</template>

<script setup>
    import { onMounted, ref } from 'vue';

    const username = ref('');
    const tempUsername = ref('');
    const messages = ref([]);
    const newMessage = ref('');

    const setUsername = () => {
        username.value = tempUsername.value.trim();
        tempUsername.value = ''; 
    };

    const setMessage = () => {
        axios.post('/chat', {
            user: username.value,
            message_text: newMessage.value
        }).then(() => {
            let message = {
                user: username.value,
                message_text: newMessage.value
            }
            messages.value.push(message);
            newMessage.value = ""; 
        });
    }

     onMounted(async () => {
        window.Echo.channel('chats').listen('MessageCreated', (e) => {
            let message = {
                user: e.chat.user,
                message_text: e.chat.message_text
            }

            messages.value.push(message);
        });
     });

</script>


<style scoped>

.chat-box-input {
    padding: 10px;
    background-color: #fff;
    border-top: 1px solid #eee;
    display: flex; /* Aligns input and button side by side */
}

.chat-box-input input {
    flex-grow: 1; /* Allows input to take up available space */
    margin-right: 8px; /* Adds spacing between input and button */
    padding: 8px 10px;
    border: 1px solid #ccc;
    border-radius: 4px;
    box-sizing: border-box;
}

.chat-box-input button {
    padding: 10px 15px;
    background-color: #007bff;
    color: #ffffff;
    border: none;
    border-radius: 4px;
    cursor: pointer; /* Changes cursor to pointer on hover */
    white-space: nowrap; /* Prevents wrapping of text in the button */
}

.chat-box-input button:hover {
    background-color: #0056b3; /* Darker shade on hover for visual feedback */
}

.chat-box {
    display: flex;
    flex-direction: column;
    max-width: 320px;
    min-width: 300px;
    height: 500px;
    border: 1px solid #ccc;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    font-family: Arial, sans-serif;
    background-color: #fff;
}

.chat-box-header {
    background-color: #007bff;
    color: #ffffff;
    padding: 10px;
    text-align: center;
    font-size: 16px;
}

.chat-box-messages {
    flex: 1;
    padding: 10px;
    overflow-y: auto;
    background-color: #f9f9f9;
    display: flex;
    flex-direction: column;
}

.message {
    margin-bottom: 12px;
    padding: 8px 10px;
    border-radius: 20px;
    display: inline-block;
    max-width: 70%;
}

.current-user {
    background-color: #007bff;
    color: #ffffff;
    margin-left: auto;
    text-align: right;
    align-self: flex-end;
}

.other-user {
    background-color: #e9ecef;
    color: #333;
}

.chat-box-input {
    padding: 10px;
    background-color: #fff;
    border-top: 1px solid #eee;
}

.chat-box-input input {
    width: 100%;
    padding: 8px 10px;
    border: 1px solid #ccc;
    border-radius: 4px;
    box-sizing: border-box;
}

.input_border{
    border: 1px solid #ccc;
}
</style>
