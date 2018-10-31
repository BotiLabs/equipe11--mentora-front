<template>
  <div class="main">
    <v-chip color="orange" text-color="white" v-for="skill in skills" v-bind:key="skill.idSkill">
      {{skill.idSkillNavigation.descricao}}
    </v-chip>
  </div>
</template>

<script>
import axios from 'axios'
import SendInvite from './SendInvite'

export default {
  props: {
    mentor: {
      type: Number,
      required: false
    }
  },
  data: () => ({
    skills: null
  }),
  methods: {
    search (idMentor) {
      axios.post('https://hackitmentoriaapi.azurewebsites.net/api/SkillMentors/GetSkillsByMentor', {
        idMentor
      })
        .then(res => {
          this.skills = res.data
        })
        .catch(err => {
          console.log(err)
        })
        .finally(() => (this.isLoading = false))
    }
  },
  mounted () {
    this.search(this.mentor)
  },
  watch: {
    mentor (val) {
      if(val) {
        this.search(val)
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main {
  padding: 20px;
}
</style>
