<template>
  <div class="results">
    <v-list three-line class="results__list">
      <template v-for="(item) in items">
        <v-list-tile
          class="results__tile"
          :key="item.title"
          avatar
          @click="open(item)"
        >
          <v-list-tile-avatar class="results__avatar">
            <img :src="item.urlImage">
          </v-list-tile-avatar>

          <v-list-tile-content class="results__content">
            <v-list-tile-title v-html="item.nome + ' (' + item.cargo + ' em ' + item.cidade + ')'"></v-list-tile-title>
            <v-list-tile-sub-title v-html="item.cv"></v-list-tile-sub-title>
          </v-list-tile-content>

        </v-list-tile>
        
      </template>
    </v-list>
    
    <div v-if="items && items.length === 0" class="noResults">
      <h3>Nenhum mentor encontrado.</h3>
    </div>
     <v-dialog
      v-model="hasMentor"
      max-width="600"
    >
    <v-card v-if="mentor">
      <v-toolbar card dark color="secondary">
        <v-card-title class="headline">{{mentor.nome}}</v-card-title>
        <v-spacer></v-spacer>
        <v-btn icon dark @click.native="hasMentor = false">
          <v-icon>close</v-icon>
        </v-btn>
      </v-toolbar>
      <v-tabs
        v-model="active"
      >
      <v-tab
        v-for="n in tabs"
        :key="n"
        ripple
      >
         {{ n }}

      </v-tab>
      <v-tab-item v-if="active === 0">
        <v-card flat>
          <iframe class="video" width="560" height="315" :src="'https://www.youtube.com/embed/' + mentor.url" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
          <v-card-text>
            {{mentor.cv}}
          </v-card-text>
        </v-card>
      </v-tab-item>
      <v-tab-item v-if="active === 1">
        <Competencias :mentor="mentor.idMentor"  />
      </v-tab-item>
      <v-tab-item v-if="active === 2">
        <SendInvite :skill="skill" :mentor="mentor.idMentor" v-on:close="close()" />
      </v-tab-item>
    </v-tabs>
    </v-card>
  </v-dialog>

  <v-dialog
      v-model="successInvite"
      hide-overlay
      persistent
      width="300"
    >
      <v-card
        color="primary"
      >
        <v-card-text style="text-align: center;">
          <p style="color: white">Seu convite foi enviado ao mentor. Aguarde a resposta via e-mail.</p>
          <v-btn @click="cĺoseSuccessInvite()">Ok</v-btn>

        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
  
</template>

<script>
import axios from 'axios'
import SendInvite from './SendInvite'
import Competencias from './Competencias'

export default {
  components: {
    SendInvite,
    Competencias
  },
  props: {
    skill: {
      type: Number,
      required: false
    }
  },
  data: () => ({
    active: 0,
    items: null,
    hasMentor: false,
    mentor: null,
    successInvite: false,
    tabs: ['Apresentação', 'Competências', 'Solicitar mentoria']
  }),
  methods: {
    search (idSkill) {
      axios.post('https://hackitmentoriaapi.azurewebsites.net/api/mentores/GetMentoresBySkill', {
        idSkill
      })
        .then(res => {
          this.items = res.data
        })
        .catch(err => {
          console.log(err)
          this.items = [
            {
              nome: 'teste'
            }
          ]
        })
        .finally(() => (this.isLoading = false))
    },
    open (item) {
      this.mentor = item
      this.hasMentor = true
      this.active = 0
    },
    close () {
      this.mentor = null
      this.hasMentor = false
      this.active = 0
      this.successInvite = true
    },
    cĺoseSuccessInvite () {
      this.successInvite = false
    }
  },
  mounted () {
    this.search(this.skill)
  },
  watch: {
    skill (val) {
      if(val) {
        this.search(val)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.results {
  background: white;
  padding: 0 0 0 150px;
  &__tile {
    margin: 12px 0;
  }
  &__content {
    max-width: 570px;
  }
  &__avatar {
    > div {
      width: 75px !important;
      height: 75px !important;
      margin: 20px 20px 0 0;
      img {
        object-fit: cover;
        object-position: center;
      }
    }
  }
}
.noResults {
  padding: 20px 0 40px 0;
}
.video {
  margin: 18px 18px 0 18px;
}
</style>
