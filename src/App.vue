<template>
    <div id="app">
        <div>
            <label for="pan">Pan:</label>
            <input id="pan" type="number" v-model="pan">

            <label for="tilt">tilt:</label>
            <input id="tilt" type="number" v-model="tilt">
            <label for="dest">Dest:</label>
            <input id="dest" type="number" v-model="destination">

            <label for="delay">Time:</label>
            <input id="delay" placeholder="seconds" type="number" v-model="delay">
            <label for="name">Macro name:</label>
            <input id="name"  v-model="macroName">
        </div>

        <button v-on:click="get()">Komendy</button>
        <button v-on:click="getMacros()">Makra</button>
        <br/>
        <div v-if="!isMacro">
            <div>
                <button v-for="commandName in commands " v-on:click="add(commandName)">{{commandName}}</button>
            </div>
            <div>
                <ul>
                    <li v-for="msg in  messages">{{printMsg(msg)}}</li>
                </ul>
            </div>
            <button v-on:click="send()">Wyślij</button>
            <button v-on:click="saveMacro()">Dodaj macro</button>

            <br/>
        </div>
        <div v-else-if="isMacro">
            <macro v-for="m in macros" v-bind:macro="m"></macro>
        </div>
        <br/>
        <ul>
            <li v-for="response in  responses">{{response}}</li>
        </ul>


    </div>
</template>

<script>
    import Macro from './components/Macro.vue'
    import axios from 'axios'

    export default {
        name: 'app',
        components: {
            Macro
        },
        data() {
            return {
                commands: [],
                pan: 1,
                tilt: 1,
                source: 0,
                destination: 1,
                messages: [],
                responses: [],
                delay: null,
                macroName: "",
                macros: [],
                isMacro: false
            }
        },
        methods: {
            printMsg(msg) {
                return `${msg.content} ${msg.source} -> ${msg.dest}`
            },
            add(name) {
                let json = {
                    source: parseInt(0),
                    dest: parseInt(this.destination),
                    p: parseInt(this.pan),
                    t: parseInt(this.tilt),
                    content: name,
                    delay: parseInt(this.delay)
                };
                this.messages.push(json)
            },
            send() {
                console.log(this.messages);
                axios.post("http://localhost:8080/commands", this.messages).then(() => {
                    console.log(this.messages);
                    this.messages = []
                })
            },
            get() {
                console.log("test");
                axios.get("http://localhost:8080").then((comands) => {
                    this.isMacro = false;
                    this.commands = comands.data
                })

            },
            saveMacro() {
                let macro = {
                    name: this.macroName,
                    delay: this.delay,
                    messages: this.messages
                };
                axios.post("http://localhost:8080/macros", macro).then(()=>this.messages = [])
            },
            getMacros() {
                axios.get("http://localhost:8080/macros").then((macros) => {
                    this.isMacro = true;
                    this.macros = macros.data
                })

            }
        },
        mounted() {
            setInterval(() => {
                axios.get("http://localhost:8080/responses").then((response) => this.responses = response.data)
            }, 1000)
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
