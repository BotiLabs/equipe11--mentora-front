<template>
  <div class="searchBox" :class="{'searchBox--slim': model}">
    <img src="@/assets/logo.png" />
    <h1>Quero ser mentorado em:</h1>
    <v-autocomplete class="autocomplete"
      v-model="model"
      :items="items"
      :loading="isLoading"
      :search-input.sync="search"
      chips
      clearable
      hide-details
      hide-selected
      item-text="descricao"
      item-value="idSkill"
      label="Digite aqui para pesquisar..."
      solo
      v-on:change="updateModel()"
    >
    </v-autocomplete>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  props: {
    skill: {
      type: Number,
      required: false
    }
  },
  data: () => ({
    model: null,
    isLoading: false,
    items: [],
    search: null
  }),
  methods: {
    updateModel () {
      this.$emit('update',this.model)
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

<style lang="scss" scoped>
  .searchBox {
    text-align: center;
    margin: 0 auto;
    max-width: 600px;
    &--slim {
      max-width: 100%;
      display: flex;
      justify-content: start;
      align-items: center;
      img {
        height: 120px;
        margin: 0 30px 0 15px;
      }
      h1 { display: none; }
      .autocomplete {
        max-width: 660px;
      }
    }
  }


  h1 {
    margin: 0 0 20px 0;
  }
</style>
