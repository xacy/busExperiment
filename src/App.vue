<template>
  <div id="app">
    <div class="columns is-centered content">
      <div class="column is-half">
        <h1>Consulta rápida de tiempos de autobuses</h1>
      </div>
    </div>
    <div class="columns is-centered">
      <div class="column is-half">
        <div class="field">
          <label class="label">Número de parada: </label>
          <div class="control">
            <input type="number" class="input" v-model="stopNumber"/>
          </div>

        </div>
      </div>
    </div>
    <div class="columns is-centered">
      <div class="column is-half">
        <div class="control">
          <button @click.prevent="queryStop" class="button is-link">Consultar</button>
        </div>
      </div>
    </div>
    <stop-info :stopInfo="queriedStop"></stop-info>
    <recent-stops :recents="recentStops"></recent-stops>
  </div>
</template>

<script>
const cheerio = require('cheerio');
import axios from 'axios';
import StopInfo from './components/StopInfo';
import RecentStops from './components/RecentStops';
import { eventBus } from './main.js';
export default {
  name: 'app',
  components: {
    StopInfo,
    RecentStops
  },
  data () {
    return {
      msg: 'Auvasa times',
      recent: [],
      stopNumber: '',
      queriedStop: { stopNumber: 0 , info: [{line: '-', name: 'Introduzca un nº de parada', time: '-'}]},/*{ time: 0, name: 'Aún no se ha consultado', line:0 },*/
      baseUrl: 'http://www.auvasa.es/parada.asp?codigo=',
      recentStops: [],
      maxRecents: 5
    }
  },
  methods:{
    queryStop(){
      //console.log(this.$http);
      this.queriedStop={stopNumber: 0 , info: []};
      this.queriedStop.stopNumber=this.stopNumber;
      this.queriedStop.info=[];
      this.queriedStop.lastQuery=new Date();
      let self=this;
      axios.get(this.baseUrl+this.stopNumber, { crossdomain: true }).then(function(response) {
        //console.log("in response"+response);
        let someData = response.data;
        //console.log(someData);
        const stopHtml = cheerio.load(someData);
        self.queriedStop.street=stopHtml('div.col_three_fifth').find('h5').text();
        self.queriedStop.street=self.queriedStop.street.substring(0,self.queriedStop.street.indexOf('('))
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
          if(lines.line!=undefined){
            self.queriedStop.info.push(lines);
          }

        });
        //console.log(self.queriedStop);
        if(!self.isInRecents(self.queriedStop)){
          self.recentStops.push(self.queriedStop);
          if(self.recentStops.length>self.maxRecents){
            self.recentStops.splice(0,1);
          }
          localStorage.setItem('recentStops', JSON.stringify(self.recentStops));
        }
        else{
          self.recentStops=self.recentStops;
        }
        //console.log("Recents: ");
        //console.log(this.recentStops);
      }).catch(function (error){
        console.log('axios error'+error);
      });
    },
    isInRecents(newStop){
      for (let i = 0; i < this.recentStops.length; i++) {
        if (this.recentStops[i].stopNumber === newStop.stopNumber) {
          this.recentStops[i].lastQuery=new Date();
          let temp=this.recentStops[i];
          this.recentStops.splice(i,1);
          this.recentStops.push(temp);
          //console.log(this.recentStops[i].lastQuery);
          return true;
        }
      }
      return false;
    }
  },
  created(){
    eventBus.$on('stopWasClicked',(data) => {
      this.stopNumber=data;
      this.queryStop();
    });
    if (localStorage.getItem("recentStops") === null) {
      localStorage.setItem('recentStops', JSON.stringify(this.recentStops));
    }
    else {
      console.log("already set");
      this.recentStops = JSON.parse(localStorage.getItem('recentStops'));

    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;*/
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
