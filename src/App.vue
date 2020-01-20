<template>
  <div id="app">

    <header>
      <button @click="prev">previous</button>
      <select v-model="current" @change="fromDrop">
        <option v-for="item in list" :key="item">{{item}}</option>
      </select>
      <button @click="next">next</button>
    </header>

    <div class="hor">
      <div id="viz"></div>
      <div class="edit">
        <textarea v-model="def"></textarea>
      </div>
    </div>

  </div>
</template>

<script>
import embed from 'vega-embed'
import axios from 'axios'
import list from './list.js'

const BASEURL = 'https://vega.github.io/vega-lite/'

export default {
  data(){
    return{
      def: null,
      list: list,
      current:null,
      index:0,
    }
  },
  watch:{
    def(v){
      if(v) this.draw()
    }
  },
  methods:{
    prev(){
      this.index--
      this.current = this.list[this.index]
      this.load()
    },
    next(){
      this.index++
      this.current = this.list[this.index]
      this.load()
    },
    fromDrop(){
      this.index = this.list.indexOf(this.current)
      this.load()
    },
    async load(){
      let src = await axios(`${BASEURL}examples/${this.current}`)

      let ob = src.data
      if(ob.data.url){
        ob.data.url = BASEURL + ob.data.url
      }
      this.def = JSON.stringify(ob, true, 2)
    },
    async draw(){
      let struct = JSON.parse(this.def)

      struct.width = 'container'
      struct.height = 'container'

      await embed('#viz', struct, {actions:false})
    }
  }
}
</script>




<style>
*{
  box-sizing: border-box;
}
html, body{
  width: 100%;
  height: 100%;
  overflow: hidden;
  padding: 0;
  margin: 0;
}


</style>

<style scoped>
header{
  display: flex;
  justify-content: space-between;
  padding:.5rem;
  background-color: #eee;
}
.hor{
  display: flex;
  width: 100%;
  height: 100%;
}

#app{
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
}
#app .hor > div{
  flex:1;
}
textarea{
  width: 100%;
  height: 100%;
  padding: .5rem;
  border:none;
  border-left:1px solid #ddd;
  color:#999;
}
input{
  width: 100%;
}
#viz{
  overflow: hidden;
}

#app .hor > #viz{
  flex:1;
}
</style>