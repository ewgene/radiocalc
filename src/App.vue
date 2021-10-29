<script>
import HelloWorld from './components/HelloWorld.vue'
import list from './rn.json'

export default {
  components: {
    HelloWorld
  },
  data: () => {
    return {
      radioNucList: [],
      list: list
    }
  },
  mounted: function () {
    for(let i = 0; i < this.list.length; i++) {
      if(this.list[i].D_val)
        this.radioNucList.push(this.list[i])
    }
  }

}
</script>

<template>
  <HelloWorld :radioNucList="radioNucList" />
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
