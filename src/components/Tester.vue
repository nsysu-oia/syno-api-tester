<template>
  <h1>{{ msg }}</h1>
  <a href="https://global.download.synology.com/download/Document/Software/DeveloperGuide/Package/FileStation/All/enu/Synology_File_Station_API_Guide.pdf" target="_blank">Official File Station Documentation</a>
  <div>
    <div>
      <label for="host">DSM Host </label>
      <input class="main" type="text" id="host" v-model="host" placeholder="https://xxx.xxx:5001" />
    </div>
    <div>
      <label for="request">Request </label>
      <input class="main" type="text" id="request" v-model="request" placeholder="/webapi/query.cgi" />
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
    <label for="params" style="display: block">Request Parameters</label>
    <textarea readonly id="params" v-model="params" style="height: 150px" />
    <label for="response" style="display: block">Responses</label>
    <textarea readonly id="response" v-model="response" />

    <h3>Example request</h3>
    <span style="text-align: left">
      Note: Error 119: SID not found. Need to login first. <br>
      <input type="text" v-model="sid" placeholder="sid" />
      <h4>SYNO.API.Info</h4>
      <div>
        <button @click="setRequest('/webapi/query.cgi?api=SYNO.API.Info&version=1&method=query&query=SYNO.API.Info,SYNO.API.Auth')">query</button>
        Show the API info. Set `query=all` to view all available APIs. Seperate multiple APIs with a comma.
      </div>
      <h4>SYNO.API.Auth</h4>
      <div>
        <button @click="setRequest('/webapi/auth.cgi?api=SYNO.API.Auth&version=7&method=login&account='+this.acc+'&passwd='+this.pw+'&session=tester&format=sid')">login</button>
        <input type="text" placeholder="account" v-model="acc" />
        <input type="text" placeholder="password" v-model="pw" />
        Set `format=cookie` to store the token in cookie; `format=sid` to retrieve a sid and manually specify it in each authentication-required request.
      </div>
      <div>
        <button @click="setRequest('/webapi/auth.cgi?api=SYNO.API.Auth&version=7&method=logout&session=tester')">logout</button>
        Use the session name specified when login to logout.
      </div>
      <h4>SYNO.FileStation.Info</h4>
      <div>
        <button @click="setRequest('/webapi/entry.cgi?api=SYNO.FileStation.Info&version=2&method=get&_sid='+this.sid)">get</button>
        Provide File Station information.
      </div>
      <h4>SYNO.FileStation.List</h4>
      <div>
        <button @click="setRequest('/webapi/entry.cgi?api=SYNO.FileStation.List&version=2&method=list_share&_sid='+this.sid)">list_share</button>
        List all shared folders.
      </div>
      <div>
        <button @click="setRequest('/webapi/entry.cgi?api=SYNO.FileStation.List&version=2&method=list&folder_path=/share&_sid='+this.sid)">list</button>
        Enumerate files in a given folder.
      </div>
    </span>
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
      params: '',
      response: '',
      acc: '',
      pw: '',
      sid: ''
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
      var searchParams = new URLSearchParams(this.request.slice(this.request.indexOf('?') + 1))
      this.params = JSON.stringify(Object.fromEntries(searchParams.entries()), null, '  ')

      this.host = (this.host.slice(-1) === '/')
        ? this.host.slice(0, -1)
        : this.host

      axios
        .get(this.host + this.request)
        .then(({ data }) => {
          this.response = (data instanceof Object)
            ? JSON.stringify(data, null, '  ')
            : data
          if (data.data && data.data.sid) {
            this.sid = data.data.sid
          }
        })
        .catch(e => {
          this.response = e
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
  color: #1C4A7C;
  text-decoration: none;
}
div {
  padding: 5px;
}
textarea {
  width: 80%;
  height: 350px;
}
input.main {
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
