<template>
    <div id="app">
        <ListenSockets />
    </div>
</template>

<script>
  import ListenSockets from "./components/ListenSockets";
    export default {
        components :{
          ListenSockets
        }
    }

</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
