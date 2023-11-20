<script setup>
import {ref} from 'vue'

const CHARACTER_LIST = ref([
  "(Baby Peach, Baby Daisy)", "(Baby Rosalina, Lemmy)", "(Baby Mario, Baby Luigi, Dry Bones, Light Mii)", "(Toadette, Wendy, Isabelle)", "(Koopa Troopa, Lakitu, Bowser Jr.)", "(Toad, Shy Guy, Larry)", "(Cat Peach, Inkling Girl, Female Villager, Diddy Kong)", "(Peach, Daisy, Yoshi, Peachette)", "(Tanooki Mario, Inkling Boy, Male Villager)", "(Mario, Ludwig, Medium Mii)", "(Luigi, Iggy, Kamek)", "(Rosalina, King Boo, Link, Pauline)", "(Metal Mario, Gold Mario, Pink Gold Peach, Petey Piranha)", "(Waluigi, Donkey Kong, Roy, Wiggler)", "(Wario, Dry Bowser, Funky Kong)", "(Bowser, Morton, Heavy Mii)"])

const allowed_characters = ref(Array.from(Array(CHARACTER_LIST.value.length).keys())) //Get sequential array from 0 to the length of the CHARACTER_LIST

</script>


<template>
  <div>
    <v-expansion-panels>
      <v-expansion-panel>
        <v-expansion-panel-title>
          Allowed characters
        </v-expansion-panel-title>
        <v-expansion-panel-text>
          <v-checkbox v-for="(character, index) in CHARACTER_LIST" v-model="allowed_characters" :label=character :value=index color="primary" input-value="1"></v-checkbox>
        </v-expansion-panel-text>
      </v-expansion-panel>
    </v-expansion-panels>
  </div>
</template>

<style scoped>

</style>

<style>
@import url("https://cdn.jsdelivr.net/npm/@mdi/font@5.x/css/materialdesignicons.min.css");
</style>
