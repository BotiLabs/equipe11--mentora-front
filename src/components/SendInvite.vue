<template>
  <div class="main">
    <v-form ref="form" lazy-validation>
      <v-text-field
        v-model="mentorado.matriculaMentorado"
        :counter="255"
        label="Matricula"
        required
      ></v-text-field>
      <v-text-field
        v-model="mentorado.nome"
        :counter="255"
        label="Nome Completo"
        required
      ></v-text-field>
      <v-text-field
        v-model="mentorado.email"
        label="E-mail Corporativo"
        :counter="255"
        required
      ></v-text-field>
      <v-combobox
        v-model="mentorado.cargo"
        :items="jobs"
        label="Cargo"
      ></v-combobox>
      <v-combobox
        v-model="mentorado.cidade"
        :items="cities"
        label="Cidade"
      ></v-combobox>
      <v-textarea
        rows="2"
        v-model="resumo"
        label="Deixe uma mensagem para o mentor"
      ></v-textarea>
      <v-btn
        color="success"
        @click="submit"
      >
        Solicitar mentoria
      </v-btn>
    </v-form>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      success: false,
      resumo: 'Olá eu gostaria de ser mentorado por você nesta competência pois você é minha referência neste assunto.',
      mentorado: {
        matriculaMentorado: Math.floor(Math.random()*100000+1),
        nome: 'Bruno Barbosa de Souza',
        cargo: 'Gerente',
        cidade: 'Curitiba',
        email: 'brunobarbosa88171706@gmail.com'
      },
      jobs: [
        'Gerente',
        'Coordenador',
        'Supervisor',
        'Analista',
        'Programador'
      ],
      cities: [
        'Curitiba',
        'São José dos Pinhais',
        'Registro',
        'São Paulo',
        'Camaçari',
        'São Gonçalo dos Campos'
      ],
      rules: {
        email: value => {
          if(value.length > 0) {
            const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
          }
        }
      }
    }
  },
  props: {
    skill: {
      type: Number,
      required: false
    },
    mentor: {
      type: Number,
      required: false
    }
  },
  methods: {
    submit () {
      this.close()
      this.createMentorado(this.mentorado)
      .then(mentorado => {
        this.createMatch(this.skill, this.mentor, mentorado.data.idMentorado, this.resumo)
          .then(match => {
          this.sendInvite(match.data)
          .then(res => {
            this.success = false
          })
        })
      })
    },
    close () {
      this.$emit('close')
    },
    createMentorado (signup) {
      return axios.post('https://hackitmentoriaapi.azurewebsites.net/api/mentorados', signup)
    },
    createMatch (idSkill, idMentor, idMentorado, resumo) {
      return axios.post('https://hackitmentoriaapi.azurewebsites.net/api/match', {
        idSkill,
        idMentor,
        idMentorado,
        resumo
      })
    },
    sendInvite (match) {
      return axios.post('https://hackitmentoriaapi.azurewebsites.net/api/mentores/invite', match)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main {
  padding: 0 10px 10px 10px;
}
</style>
