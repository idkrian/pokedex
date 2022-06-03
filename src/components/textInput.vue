<template>
  <input type="text" name="" id="" v-model="valor"/>
  
</template>

<script>
export default {
  name: "textInput",
  data() {
    return {
      valor: null,
    };
  },
};
</script>

<style scoped>
input {
  border: 2px solid black;
  padding: 15px;
  border-radius: 10px;
  background-color: white;
  font-size: medium;
  font-weight: bold;
  text-align: center;
}
input:hover {
  outline-color: #212121;
  outline: none;
  background-color: white;
  color: black;
  box-shadow: 2px 2px black;
  backdrop-filter: blur(0px);
}
</style>