<template>
  <div id="app">
    <div v-if="!colored">
      <img :src="front[0]"><img :src="back[0]">
    </div>
    <div v-else>
      <img :src="front[1]"><img :src="back[1]">
    </div>
    <button type="button" v-on:click="shine()">{{ coloredButton }}</button>
    <h1>{{name}}</h1>
    <input type="text" v-model="name" placeholder="ポケモンの名前〜〜">
    <button type="button" v-on:click="setPokemonId()">検索</button>
    <p>タイプ {{ types[0] }} / {{ types[1] }} </p>
    <p>おもさ : {{weight}} </p>
    <p>たかさ : {{height}} </p>
    <p> No.{{dexNumber}} </p>
    <h1 style="color:red;">{{ error }}</h1>
  </div>
</template>

<script>

  import axios from 'axios'
  import poke from 'pokemon'

  console.log(poke.getName(1,'ja'))

  const pokeList = poke.all('ja')
  const typeList = [
    {'en':'normal','ja':'ノーマル'},
    {'en':'fire','ja':'ほのお'},
    {'en':'water','ja':'みず'},
    {'en':'grass','ja':'くさ'},
    {'en':'electric','ja':'でんき'},
    {'en':'ice','ja':'こおり'},
    {'en':'fighting','ja':'かくとう'},
    {'en':'poison','ja':'どく'},
    {'en':'ground','ja':'じめん'},
    {'en':'flying','ja':'ひこう'},
    {'en':'psychic','ja':'エスパー'},
    {'en':'bug','ja':'むし'},
    {'en':'rock','ja':'いわ'},
    {'en':'ghost','ja':'ゴースト'},
    {'en':'dragon','ja':'ドラゴン'},
    {'en':'dark','ja':'あく'},
    {'en':'steel','ja':'はがね'},
    {'en':'fairy','ja':'フェアリー'},
    {'en':'none','ja':'なし'}
    // {normal:'ノーマル'},
    // {fire:'ほのお'},
    // {water:'みず'},
    // {grass:'くさ'},
    // {electric:'でんき'},
    // {ice:'こおり'},
    // {fighting:'かくとう'},
    // {poison:'どく'},
    // {ground:'じめん'},
    // {flying:'ひこう'},
    // {psychic:'エスパー'},
    // {bug:'むし'},
    // {rock:'いわ'},
    // {ghost:'ゴースト'},
    // {dragon:'ドラゴン'},
    // {dark:'あく'},
    // {steel:'はがね'},
    // {fairy:'フェアリー'},
    // {none:'なし'}
  ]

  export default {
    name: 'App',
    data () {
      return {
        name: "",
        types: [,],
        translatedTypes: [,],
        error: "",
        weight: "",
        height: "",
        front: [,],
        back: [,],
        // front_shiny: "",
        // back_shiny: "",
        dexNumber: "",
        pokemonData: "",
        colored: false,
        coloredButton: "色変更",
        url: "https://pokeapi.co/api/v2/pokemon/"
      }
    },
    methods : {
      getPokemon () {
        axios.get(this.url+this.dexNumber+"/")
          .then((res)=>{
            console.log(res["data"])

            // this.types[0] = res["data"]["types"][0]["type"]["name"]
            this.types[0] = typeList.filter(type => type['en'] == res["data"]["types"][0]["type"]["name"])[0]['ja']

            if (res["data"]["types"][1]) {
              this.types[1] = typeList.filter(type => type['en'] == res["data"]["types"][1]["type"]["name"])[0]['ja']
            } else {
              this.types[1] = "none"
            }

            this.weight = res["data"]["weight"]
            this.height = res["data"]["height"]

            this.front[0] = res["data"]["sprites"]["front_default"]
            this.back[0]  = res["data"]["sprites"]["back_default"]

            this.front[1] = res["data"]["sprites"]["front_shiny"]
            this.back[1]  = res["data"]["sprites"]["back_shiny"]
            // this.front_shiny = res["data"]["sprites"]["front_shiny"]
            // this.back_shiny  = res["data"]["sprites"]["back_shiny"]

            this.colored = false
            this.coloredButton = "色違う"
          })
        // .then((json)=>{console.log(json)})
      },
      setPokemonId() {
        let flag = false
        for (let i of pokeList){
          if (this.name == i) {
            this.dexNumber = poke.getId(this.name, 'ja')
            flag = false
            break;
          } else {
            flag = true
          }
        }
        if (flag) {
          this.error = "存在しないポケモンです"
        } else {
          this.error = null
          this.getPokemon()
          this.setTypes()
        }
      },
      setTypes() {
        // let types = this.types;
        var trans = [];

        var t1 = typeList.filter(type => type['en'] == this.types[0])
        var t2 = typeList.filter(type => type['en'] == this.types[1])

        trans.push(t1[0]['ja']);
        trans.push(t2[0]['ja']);

        this.translatedTypes = trans;
      },
      // over() {
      //   if (this.colored) {
      //     this.img = this.back[1]
      //   } else {
      //     this.img = this.back[0]
      //   }
      // },
      // leave() {
      //   if (this.colored) {
      //     this.img = this.front[1]
      //   } else {
      //     this.img = this.front[0]
      //   }
      // },
      shine() {
        this.colored = !this.colored
        if (this.colored) {
          this.coloredButton = "色違わない"
        } else {
          this.coloredButton = "色違う"
        }
      }
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
