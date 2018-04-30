<template>
  <div>
    <div class="columns is-centered">
      <div class="column is-half">
        <div class="content">
          <h1>Paradas recientes consultadas</h1>
        </div>
      </div>
    </div>
    <div class="columns  is-centered">
      <div class="column is-half">
        <nav class="level is-mobile"  v-for="recent in recentStops.slice().reverse()" @click="stopItsClicked(recent.stopNumber)">
          <div class="level-item has-text-centered">
            <div>
              <p class="heading">Parada</p>
              <p class="title">{{ recent.stopNumber }}</p>
            </div>
          </div>
          <div class="level-item has-text-centered">
            <div>
              <p class="heading">Calle</p>
              <p class="title name">{{ recent.street}}</p>
            </div>
          </div>
          <div class="level-item has-text-centered">
            <div>
              <p class="heading">Última consulta</p>
              <p class="title">{{ new Date(recent.lastQuery).toLocaleDateString([], {hour: '2-digit', minute:'2-digit'})}}</p>
            </div>
          </div>
        </nav>
      </div>
    </div>
  </div>

</template>

<script>
  import { eventBus } from '../main.js';
    export default {
      props: {
        recents: {
          type: Array
        }
      },
      data() {
        return {
          recentStops: this.recents
        }
      },
      methods: {
        stopItsClicked(stopNumber){
          eventBus.clickStop(stopNumber);
        }
      }
    }
</script>

<style>
.name{
  width:9em;
  font-size:1rem!important;
}
  .level{
    cursor: pointer;
  }
</style>
