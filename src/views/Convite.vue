<template>
  <div class="convite">
    <img class="logo" src="@/assets/logo.png" />
    <div v-if="invite">
      <h1>Olá {{invite.idMentorNavigation.nome}}</h1>
      <p>
        Você recebeu um solicitação de mentoria sobre 
        <strong>{{invite.idSkillNavigation.descricao}}</strong>
        para o colaborador 
        <strong>{{invite.idMentoradoNavigation.nome}}</strong>.
      </p>
      <p class="label" v-if="invite.resumo">
        Veja a mensagem que ele deixou para você:
      </p>
      <h3 v-if="invite.resumo">"{{invite.resumo}}"</h3>
      <div v-if="invite.aceito !== 0 && invite.aceito !== 1">
        <v-btn @click="sendAnswer(1)" round dark class="accept">Aceitar</v-btn>
        <v-btn @click="sendAnswer(0)" round dark class="reject">Rejeitar</v-btn>
      </div>
      <h2 class="feedback" v-if="invite.aceito === 0 || invite.aceito == 1"
         :class="{'feedback--success':invite.aceito === 1, 'feedback--error':invite.aceito === 0 }">
        Mentoria 
        <span v-if="invite.aceito === 1">aceita</span>
        <span v-if="invite.aceito === 0">rejeitada</span>
      </h2>
      <hr />
      <div class="mentorado">
        <h4>Detalhes do Mentorado</h4>
        <p>Nome: {{invite.idMentoradoNavigation.nome}}</p>
        <p>E-mail: {{invite.idMentoradoNavigation.email}}</p>
        <p>Cargo: {{invite.idMentoradoNavigation.cargo}}</p>
        <p>Cidade: {{invite.idMentoradoNavigation.cidade}}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'convite',
  data () {
    return {
      invite: null,
      id: null
    }
  },
  methods: {
    sendAnswer (answer) {
      this.invite.aceito = answer
      axios.put('https://hackitmentoriaapi.azurewebsites.net/api/match/' + this.id, this.invite)
      .then(resp => {
        let action

        if (this.invite.aceito === 1) {
          action = 'AceitarInvite'
        } else {
          action = 'RecusarInvite'
        }
        
        if (action) {
          axios.post('https://hackitmentoriaapi.azurewebsites.net/api/mentores/' + action, {
            idMatch: this.id
          })
        }
        
      })
    }
  },
  mounted () {
    this.id = this.$route.params.id
    axios.get('https://hackitmentoriaapi.azurewebsites.net/api/match/' + this.id)
    .then(resp => {
      this.invite = resp.data
    })
  }
}
</script>

<style scoped lang="scss">
.convite {
  text-align: center;
  max-width: 450px;
  margin: 0 auto;
  padding-bottom: 100px;
}
.logo {
  height: 150px;
}
h1 {
  margin: 0 0 20px 0;
  color: #222;
}
p {
  font-size: 19px;
}
.label {
  text-transform: uppercase;
  font-size: 16px;
  font-weight: bold;
  margin: 40px 0 20px 0;
}
h3 {
  font-size: 24px;
  margin: 20px 0;
}
hr {
  margin: 20px 0;
}
h4 {
  margin: 0 0 15px 0;
}
.mentorado {
  text-align: left;
  h4 {
    text-transform: uppercase;
    font-size: 14px;
    font-weight: bold;
    margin: 20px 0 20px 0;
  }
  p {
    font-size: 17px;
    margin: 10px 0;
  }
}
.accept {
  background-color: #4caf50 !important;
  border-color: #4caf50 !important;
}
.reject {
  background-color: #ff5252 !important;
  border-color: #ff5252 !important;
}
.feedback {
  &--success {
    color: #4caf50 !important;
  }
  &--error {
    color: #ff5252 !important;
  }
}
</style>