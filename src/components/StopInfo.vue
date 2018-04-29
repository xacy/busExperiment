<template>
  <div class="info">
      <table align="center">
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
      </table>
  </div>
</template>

<script>
    export default {
      props: {
        stopInfo: Object
      }
    }
</script>

<style scoped>
.info{
  text-align: center;
}
</style>
