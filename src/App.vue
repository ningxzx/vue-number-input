<template>
  <div id="app">
       <numInput></numInput>
       <keyboard></keyboard>
  </div>
</template>

<script>
import Hello from './components/Hello'
import numInput from './components/numInput/'
import keyboard from './components/numInput/keyboard'

export default {
  name: 'app',
  components: {
    Hello,numInput,keyboard
  }
}
</script>

<style>
html,body{
  height: 100%;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  height: 100%;
}
</style>
