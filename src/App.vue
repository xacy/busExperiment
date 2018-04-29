<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ msg }}</h1>
    <h2>Essential Links</h2>
    <p>Parada: </p><input type="number" v-model="stopNumber"/>
    <button @click="queryStop">Consultar</button>
    <stop-info :stopInfo="queriedStop"></stop-info>
    <ul>
      <li><a href="https://vuejs.org" target="_blank">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank">Twitter</a></li>
    </ul>
    <h2>Ecosystem</h2>
    <ul>
      <li><a href="http://router.vuejs.org/" target="_blank">vue-router</a></li>
      <li><a href="http://vuex.vuejs.org/" target="_blank">vuex</a></li>
      <li><a href="http://vue-loader.vuejs.org/" target="_blank">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank">awesome-vue</a></li>
    </ul>
  </div>
</template>

<script>
const cheerio = require('cheerio');
import StopInfo from './components/StopInfo';
export default {
  name: 'app',
  components: {
    StopInfo
  },
  data () {
    return {
      msg: 'Auvasa times',
      recent: [],
      stopNumber: '',
      queriedStop: { stopNumber: 0 , info: []},/*{ time: 0, name: 'Aún no se ha consultado', line:0 },*/
      baseUrl: 'http://www.auvasa.es/parada.asp?codigo=',
      recentStops: []
    }
  },
  methods:{
    queryStop(){
      console.log(this.$http);
      this.queriedStop={stopNumber: 0 , info: []};
      this.queriedStop.stopNumber=this.stopNumber;
      this.queriedStop.info=[];
      let self=this;
      this.$http.get(this.baseUrl+this.stopNumber).then(response => {
        // get body data
        let someData = response.body;
        console.log(someData);
        const stopHtml = cheerio.load(someData);
        stopHtml('table').find('tr').each(function(i,element){
          let lines = {};
          stopHtml(element).find('td').each(function (i,element) {
            console.log(stopHtml(element).text());
            if(i==0){
              lines.line=stopHtml(element).text();
            }
            else if(i==3){
              lines.name=stopHtml(element).text();
            }
            else if(i==4){
              lines.time=stopHtml(element).text();
            }
          });
          self.queriedStop.info.push(lines);
        });
        console.log(self.queriedStop);
        this.recentStops.push(self.queriedStop);
        console.log("Recents: ");
        console.log(this.recentStops);
      }, response => {
        // error callback
      });

    }
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

h1, h2 {
  font-weight: normal;
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
</style>
