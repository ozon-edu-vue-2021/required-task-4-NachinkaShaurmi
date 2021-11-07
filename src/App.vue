<template>
  <div id="app">
    <Form />
  </div>
</template>

<script>
import Form from "./components/Form.vue";

export default {
  name: "App",
  components: {
    Form,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
  background-color: #fafafa;
  padding: 24px;
  box-sizing: border-box;
}

html,
body,
#app {
  height: 100%;
}

* {
  box-sizing: border-box;
}

input {
  max-width: 600px;
  border-radius: 6px;
  border: 1px solid #ccc;
  padding: 5px;
}

input:focus {
  outline: none;
}

li {
  list-style-type: none;
  padding: 5px;
  cursor: pointer;
}

ul {
  margin: 0;
  padding: 0;
  border-radius: 6px;
  border: 1px solid #ccc;
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 600px;
}
</style>
