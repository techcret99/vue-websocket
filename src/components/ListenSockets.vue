<template>
    <div>
        <button @click="disconnect" v-if="status === 'connected'">Disconnect</button>
        <button @click="connect" v-if="status === 'disconnected'">Connect</button>
        {{ status }}
        <br/><br/>
        <div v-if="status === 'connected'">
            <form @submit.prevent="sendMessage" action="#">
                <input v-model="message">
                <button type="submit">Send Message</button>
            </form>
            <ul id="logs">
                <li v-bind:key="log" v-for="log in logs" class="log">
                    {{ log.event }}: {{ log.data }}
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                message: "",
                logs: [],
                status: "disconnected"
            }
        },
        created() {
            this.connect()
        },

        methods: {
            connect() {
                this.socket = new WebSocket("wss://echo.websocket.org");
                this.socket.onopen = () => {
                    this.status = "connected";
                    this.logs.push({event: "Connected to", data: 'wss://echo.websocket.org'});


                    this.socket.onmessage = ({data}) => {
                        this.logs.push({event: "Received message", data});
                    };
                };
            },
            disconnect() {
                this.socket.close();
                this.status = "disconnected";
                this.logs = [];
            },
            sendMessage() {
                this.socket.send(this.message);
                this.logs.push({event: "Sent message", data: this.message});
                this.message = "";
            }
        }
    }
</script>