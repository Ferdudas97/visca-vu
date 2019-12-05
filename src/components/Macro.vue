<template>
    <div>
        <button v-on:click="isClicked = !isClicked">{{macro.name}}</button>
        <div v-if="isClicked">
            <ul>
                <li v-for="msg in  macro.messages">{{printMsg(msg)}}</li>
            </ul>
            <button v-on:click="send()">Wykonaj</button>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'

    export default {
        name: 'Macro',
        props: ["macro"],
        data() {
            return {
                isClicked: false
            }
        },
        methods: {
            printMsg(msg) {
                return `${msg.content} ${msg.source} -> ${msg.dest}`
            },
            send() {
                axios.post(`http://localhost:8080/macros/${this.macro.name}`)
            }
        }

    }
</script>
