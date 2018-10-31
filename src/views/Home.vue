<template>
  <v-app class="app">
    <v-content>
      <Search :skill="idSkill" v-on:update="idSkill = $event" />
      <Mentores v-if="idSkill" :skill="idSkill" />
    </v-content>
  </v-app>
</template>

<script>
import Search from './../components/Search'
import Mentores from './../components/Mentores'

export default {
  name: 'App',
  components: {
    Search,
    Mentores
  },
  data () {
    return {
      idSkill: null
    }
  }
}
</script>

<style >
  .app {
    background: #EEE !important;
  }
</style>
