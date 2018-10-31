<template>
  <div>
    <v-layout justify-center>
      <v-flex xs12 sm10 md8 lg6>
        <h1 style="margin-top: 20px;text-align:center;">Inscrição para Mentores</h1>
        <v-card ref="form" class="cards">
          <v-card-text>
            <h2>Introdução</h2>
            <v-form ref="form" lazy-validation>
               <v-textarea
                v-model="signup.resumo"
                rows="3"
                label="Conte um pouco sobre suas experiencias e habilidades"
              ></v-textarea>
            </v-form>
          </v-card-text>
        </v-card>
        <v-card ref="form" class="cards">
          <v-card-text>
            <h2 style="margin-bottom: 10px">Competências</h2>
            <v-autocomplete class="autocomplete"
              v-model="model"
              :items="items"
              :loading="isLoading"
              :search-input.sync="search"
              chips
              clearable
              hide-details
              hide-selected
              multiple
              item-text="descricao"
              item-value="idSkill"
              label="Digite aqui para pesquisar..."
              solo
            >
            </v-autocomplete>
          </v-card-text>
        </v-card>
        <v-card ref="form" class="cards">
          <v-card-text>
            <h2>Informações Adicionais</h2>
            <v-form ref="form" lazy-validation>
              <v-text-field
                v-model="signup.matricula"
                :counter="255"
                label="Matricula"
                required
              ></v-text-field>
              <v-text-field
                v-model="signup.nome"
                :counter="255"
                label="Nome Completo"
                required
              ></v-text-field>
              <v-text-field
                v-model="signup.email"
                label="E-mail Corporativo (você receberá convites por lá)"
                :counter="255"
                required
              ></v-text-field>
              <v-combobox
                v-model="signup.cargo"
                :items="jobs"
                label="Cargo"
              ></v-combobox>
              <v-combobox
                v-model="signup.cidade"
                :items="cities"
                label="Cidade"
              ></v-combobox>
              <v-btn
                class="accept"
                color="success"
                @click="submit"
              >
                Cadastrar
              </v-btn>
            </v-form>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  data () {
    return {
      signup: {
        matriculaMentor: null,
        nome: null,
        email: null,
        cargo: null,
        cidade: null,
        cv: null,
        url: null,
        urlImage: null,
        disponivel: 1
      },
      model: null,
      isLoading: false,
      items: [],
      search: null,
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
  methods: {
    submit () {

    }
  },
  watch: {
    search (val) {
      // Items have already been loaded
      if (this.items.length > 0) return

      this.isLoading = true

      // Lazily load input items
      axios.post('https://hackitmentoriaapi.azurewebsites.net/api/skills/GetSkillByName', {
        descricao: val
      })
        .then(res => {
          this.items = res.data
        })
        .catch(err => {
          console.log(err)
        })
        .finally(() => (this.isLoading = false))
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
.cards {
  margin: 20px 0;
}
.accept {
  background-color: #4caf50 !important;
  border-color: #4caf50 !important;
}
</style>
