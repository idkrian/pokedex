<template>
  <img src="../img/Pokemon_animated.webp" alt="" />
  <div class="home">
    <h1>Pokedéx</h1>
    <div class="submit">
      <TextInput v-model="valor" @keyup.enter="pesquisar()" @keyup.capture="listaPokemon()"  list="nomePokemon"/>
      <datalist id="nomePokemon"></datalist>
      <Button name="Procurar Pokemon" @click="pesquisar()"></button>
    </div>
    <div v-show="img" class="pokemonContainer">
      <img :src="img" alt="" class="pokemonImage" />
      <div class="pokemonProfile">
        <p>#{{ order }}</p>
        <p class="pokemonName">{{ name }}</p>
        <p v-show="type" v-for="(tp, key) in type" :key="key">
          {{ tp.name }}
        </p>
      </div>
    </div>
    <p class="subTitle" v-show="weight">Pokedéx Data</p>
    <div class="pokemonAllStats">
      <div class="pokemonStats">
        <div>
          <div class="pokemonDataTable" v-show="ability">
            <p class="dataTitle">Skills:</p>
            <p class="pokemonData" v-for="(ab, key) in ability" :key="key" v-show="ability">
              {{ ab.name }}
            </p>
          </div>
        </div>
        <div class="pokemonDataTable" v-show="weight">
          <p class="dataTitle">Weight:</p>
          <p class="pokemonData" v-show="weight">{{ weight }}kg</p>
        </div>
        <div class="pokemonDataTable" v-show="weight">
          <p class="dataTitle">Height:</p>
          <p class="pokemonData" v-show="height">{{ height }}m</p>
        </div>
      </div>

      <div class="pokemonStats2">
        <div class="pokemonStatsTable">
          <div class="dataTitle" v-for="(name, key) in statsName" :key="key">
            {{ name.name }}:
          </div>
        </div>
        <div class="pokemonStatsTableValue">
          <div class="pokemonData" v-for="(value, key) in statsValue" :key="key">
            {{ value.base_stat }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import analyze from "rgbaster";
import Button from "@/components/Button.vue";
import TextInput from "@/components/textInput.vue";

export default {
  name: "HomeView",
  data() {
    return {
      valor: null,
      name: null,
      ability: null,
      height: null,
      img: null,
      weight: null,
      type: null,
      bgColor: null,
      pokemons: null,
      order: null,
      statsName: null,
      statsValue: null,
    };
  },
  methods: {
    async pesquisar() {
      document.querySelector('#nomePokemon').innerHTML = ''
      const reqPokemon = await fetch(
        `https://pokeapi.co/api/v2/pokemon/${this.valor.toLowerCase()}`
      );
      const resPokemon = await reqPokemon.json();

      //Pegando a cor predominante do pokemon
      const backgroundColor = await analyze(resPokemon.sprites.front_default);
      this.bgColor = await backgroundColor[0].color;

      //Passando os valoresPokemon da api para as variáveis
      this.name = resPokemon.name;
      this.ability = resPokemon.abilities.map((value) => value.ability);
      this.type = resPokemon.types.map((value) => value.type);
      this.height = (resPokemon.height * 10) / 100;
      this.img = resPokemon.sprites.other["official-artwork"].front_default;
      this.weight = resPokemon.weight / 10;
      this.order = resPokemon.order;
      this.statsValue = resPokemon.stats.map((value) => value);
      this.statsName = resPokemon.stats.map((value) => value.stat);
    },
    async listaPokemon() {
      if (window.localStorage.getItem("pokemons") == null) {
        const reqPokemon = await fetch(
          `https://pokeapi.co/api/v2/pokemon?limit=1126`
        );
        const resPokemon = await reqPokemon.json();
        const nomePokemon = resPokemon.results.map((value) => value.name);
        window.localStorage.setItem("pokemons", JSON.stringify(nomePokemon));
      }
      if (this.valor.length >= 3) {
        const pokemons = JSON.parse(window.localStorage.getItem("pokemons")).filter(value=>{
          return value.toLowerCase().includes(this.valor.toLowerCase())
        })
        console.log(pokemons)
        const pokemonList = document.querySelector('#nomePokemon')
        pokemonList.innerHTML = ''
        pokemons.forEach(value=>{
          const option = document.createElement('option')
          option.value = value
          pokemonList.appendChild(option)
        })
      }else{
        document.querySelector('#nomePokemon').innerHTML = ''
      }
    }
  },
  mounted() { },
  components: {
    Button,
    TextInput
},
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
}

.pokemonAllStats {
  display: flex;
  flex-direction: row;
  align-items: flex-start;
}

.pokemonStats2 {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  width: 280px;
  margin: 0 auto;
}

.pokemonStats {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  width: 280px;
  margin: 0 auto;
}

.pokemonStats p {
  margin: 5px;
}

.pokemonDataTable {
  display: flex;
}

.submit {
  display: flex;
  justify-content: center;
  flex-direction: column;
  width: 250px;
  align-items: center;
  margin: 2px auto;
}

.pokemonData {
  text-transform: capitalize;
}

.subTitle {
  color: v-bind("bgColor");
  font-weight: bold;
}

.dataTitle {
  font-weight: bold;
  display: flex;
  text-transform: capitalize;
}

.pokemonProfile {
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-transform: capitalize;
  font-weight: bold;
  width: 180px;
  font-size: 14px;
  color: black;
}

.pokemonName {
  font-size: 24px;
}

.home {
  min-height: 150px;
  border-style: solid;
  width: 500px;
  margin: 0 auto;
  background-color: white;
  background-image: linear-gradient(var(--9ea40744-bgColor) 10%, white 80%);
}

.pokemonContainer {
  margin-top: 15px;
  display: flex;
  justify-content: center;
  border-radius: 10px;
  /* background-image: linear-gradient(v-bind("bgColor") 60%, white 100%); */
}

.pokemonImage {
  width: 200px;
  height: 200px;
}
</style>
