<template>
  <h1>{{ msg }}</h1>
  <div>
    <div>
      <label for="host">DSM Host </label>
      <input type="text" id="host" v-model="host" placeholder="https://xxx.xxx:5001" />
    </div>
    <div>
      <label for="request">Request </label>
      <input type="text" id="request" v-model="request" placeholder="/webapi/query.cgi" />
    </div>
    <!--
    <div>
      <label for="acc">DSM Account </label>
      <input type="text" id="acc" v-model="acc" placeholder="admin" />
    </div>
    <div>
      <label for="pw">DSM Password </label>
      <input type="text" id="pw" v-model="pw" placeholder="***" />
    </div>
    -->
    <div>
      <button @click="makeRequest">Make request</button>
    </div>
    <textarea v-model="result" />

    <h3>Example request</h3>
    <div>
      <button @click="setRequest('/webapi/query.cgi?api=SYNO.API.Info&version=1&method=query&query=SYNO.API.Info')">SYNO.API.Info</button>
      Show the API info. Set `query=all` to view all available APIs.
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Tester',
  props: {
    msg: String
  },
  data () {
    return {
      host: 'https://studyabroad.nsysu.edu.tw:5001',
      request: '',
      result: ''
    }
  },
  methods: {
    setRequest (text) {
      this.request = text
    },
    makeRequest () {
      if (!this.host || !this.request) {
        alert('DSM Host or Request cannot be empty!')
        return
      }

      this.host = (this.host.slice(-1) === '/')
        ? this.host.slice(0, -1)
        : this.host

      axios
        .get(this.host + this.request)
        .then(({ data }) => {
          this.result = (data instanceof Object)
            ? JSON.stringify(data, null, '  ')
            : data
        })
        .catch(e => {
          this.result = e
        })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
div {
  padding: 5px;
}
textarea {
  width: 80%;
  height: 350px;
}
input {
  width: 80%;
  font-size: 16px;
  line-height: 24px;
  padding: 5px 10px;
  border: .0625rem solid #d2d2d7;
  border-radius: .25rem;
  color: #1d1d1f;
  box-sizing: border-box;
  font-family:inherit;
}
button {
  background-color: #1C4A7C; /* Green */
  border: none;
  border-radius: 25px;
  color: white;
  text-align: center;
  text-decoration: none;
  cursor: pointer;
  padding: 5px 20px;
  font-family:inherit;
  font-size: 17px;
}
</style>
