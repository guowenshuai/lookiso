<template>
  <div id="app">
    <div class="banner">
      <h1 style="padding: 20px">Linux ISO Image Downloads</h1>
    </div>    
    <div class="background">
      <ISOLIST/>
    </div>
    <!-- <img class="banner" alt="iso logo" src="./assets/static/img/logo.png"> -->
  </div>
</template>

<script>
import ISOLIST from './components/IsoList.vue'

export default {
  name: 'App',
  components: {
    ISOLIST
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0px;
}
.banner {
    background: -moz-linear-gradient(top, #F0FFFF 0%, #ffffff 100%);
    background: -webkit-gradient(linear, left top, left bottom, color-stop(0%,#000ff0), color-stop(100%,#ffffff));
    background: -webkit-linear-gradient(top, #F0FFFF 0%,#ffffff 100%);
    background: -o-linear-gradient(top, #F0FFFF 0%,#ffffff 100%);
    background: -ms-linear-gradient(top, #F0FFFF 0%,#ffffff 100%);
    background: linear-gradient(to bottom, #F0FFFF 0%,#ffffff 100%);
    width:100%;
    height:100px;
    font-size: 20px;
    text-shadow: 3px 3px 3px gray, 0px 0px 2px black; 
    color: white;
}

.background {
    background: -moz-linear-gradient(bottom, #F3FFFF 0%, #ffffff 100%);
    background: -webkit-gradient(linear, left bottom, left top, color-stop(0%,#000ff0), color-stop(100%,#ffffff));
    background: -webkit-linear-gradient(bottom, #F3FFFF 0%,#ffffff 100%);
    background: -o-linear-gradient(bottom, #F3FFFF 0%,#ffffff 100%);
    background: -ms-linear-gradient(bottom, #F3FFFF 0%,#ffffff 100%);
    background: linear-gradient(to top, #F3FFFF 0%,#ffffff 100%);
    width: 100%;
    height: 100%;
    position: absolute  
}

</style>
