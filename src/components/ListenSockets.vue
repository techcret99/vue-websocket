<template>
    <div class="container">

        <div class="card">
            <div class="card-header"><h1>Transcription Engine Web Interface</h1></div>
            <div class="card-header">
                <form>
                    <div class="alert alert-danger" v-if="this.invalidFileUpload" role="alert">
                        Please upload wav format file.
                    </div>
                    <h3>Upload file</h3>
                    <div class="form-check">
                        <input type="file" name="file" id="file" ref="file" v-on:change="handleFileUpload()"/>
                        <label class="form-check-label" for="file">Upload file</label>
                    </div>
                    <button type="button" v-on:click="submitFile()" class="btn btn-success">Submit</button>
                </form>

            </div>
            <div class="card-body">
                <div v-if="status === 'connected'">
                    <form @submit.prevent="sendMessage" action="#">
                        <input v-model="message">
                        <button type="submit">Send Message</button>
                    </form>
                    <ul id="logs">
                        <li v-bind:key="index" v-for="(log,index) in logs" class="log">
                            {{ log.event }}: {{ log.data }}
                        </li>
                    </ul>
                </div>
            </div>
            <div class="card-footer">
                <div class="alert alert-danger" v-if="status === 'disconnected'" role="alert">
                    Web Socket status - DISCONNECTED!
                </div>
                <div class="alert alert-success" v-if="status === 'connected'" role="alert">
                    Web Socket status - CONNECTED!
                </div>
            </div>
        </div>


        <br/><br/>
    </div>
</template>

<script>
    import axios from "axios"

    export default {
        data() {
            return {
                message: "",
                logs: [],
                status: "disconnected",
                file: '',
                invalidFileUpload: false
            }
        },
        /*created() {
            this.connect()
        },*/

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
            },

            submitFile() {
                let self = this;

                let formData = new FormData();

                formData.append('file', this.file);

                axios.post('http://127.0.0.1:5000/transcribe/upload',
                    formData,
                    {
                        headers: {
                            'Content-Type': 'multipart/form-data'
                        }
                    }
                ).then(function (response) {
                    console.log(response.data);
                    self.connect();
                }).catch(function (error) {
                    alert(error);
                });


            },

            handleFileUpload() {
                if (/\.(wav)$/i.test(this.$refs.file.files[0].name)) {
                    this.invalidFileUpload = false;
                    this.file = this.$refs.file.files[0];
                } else {
                    this.invalidFileUpload = true;
                }
            }
        }
    }
</script>