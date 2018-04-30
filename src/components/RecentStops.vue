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
        <nav class="level is-mobile"  v-for="recent in recentStops">
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
              <p class="title">{{ recent.lastQuery.toLocaleDateString([], {hour: '2-digit', minute:'2-digit'})}}</p>
            </div>
          </div>
        </nav>
      </div>
    </div>
  </div>

</template>

<script>
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
      }
    }
</script>

<style>
.name{
  width:9em;
  font-size:1rem!important;
}
</style>
