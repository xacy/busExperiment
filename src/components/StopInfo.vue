<template>
  <div class="info">
    <div class="columns is-centered">
      <div class="column is-half">
        <table align="left" class="table is-striped">
          <thead>
          <th>
            Línea
          </th>
          <th>
            Destino
          </th>
          <th>
            Tiempo estimado.
          </th>
          </thead>
          <tbody>
          <tr v-for="lineInfo in stopInfo.info">
            <td>
              {{ lineInfo.line}}
            </td>
            <td>
              {{ lineInfo.name }}
            </td>
            <td>
              {{ lineInfo.time }}
            </td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
    export default {
      props: {
        stopInfo: {
          type: Object
        }
      }
    }
</script>

<style>
.info{
  /*text-align: center;*/
}
</style>
