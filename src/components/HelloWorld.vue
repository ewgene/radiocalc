<script>
import { ref } from 'vue'


export default {
  props: [
    'radioNucList'
  ],
  data () {
    return {
      list: this.radioNucList,
      rnCount: 0,
      rnCalc: [],
      rnValue: null,
      rnAct: null,
      messages: {
        1: "Категория опасности 1: Чрезвычайно опасно для человека",
        2: "Категория опасности 2: Очень опасно для человека",
        3: "Категория опасности 3: Опасно для человека",
        4: "Категория опасности 4: Опасность для человека маловероятна"
      },
      message: null,
      danger: null
    }
  },
  computed: {
    calcAcrivity: function () {
      for(let i=0; i<this.rnCalc.length; i++) {     
        this.rnAct += this.rnCalc[i].AM/this.rnCalc[i].D_val
      }
      console.log(this.rnAct)
      return this.rnAct
    }
  },
  methods: {
    addRn() {
      consose.log(this.rnCalc)
    },
    findRn() {
      console.log(this.rnValue)
      for(let i = 0; i<this.list.length; i++) {
        if(this.rnValue == this.list[i].Kod_RN
          || this.rnValue == this.list[i].Name_RN_Lat)
          { 
            this.rnCalc.push(JSON.parse(JSON.stringify(this.list[i])))
            console.log(JSON.parse(JSON.stringify(this.list[i])))
            return
          }
      }
      return this.rnCalc
    },
    displayResult() {
      this.rnValue = null
      this.message = this.messages[this.danger-1]
    }
  }
}

</script>

<template>
  <div id="Calc">
    <h1>Расчёт категории ЗРИ</h1>
    <div class="addRn">
      <i class="fa fa-plus"
        @click="addRn()" />
    </div>
    <div class="rnList">
      <input class="element"
        v-model="rnValue"
        @change="findRn()" />
        <div v-if="this.rnCalc.length">
          <div class="rn" v-for="item in rnCalc" :key="item.id">
            <p> <span>{{item.Name_RN_Lat}}</span> <span>{{item.D_val}}</span></p>
          </div>
          <button class="submit"
            @click="calcActivity">
            Рассчитать категорию ЗРИ
          </button>
          <div class="result">

          </div>
        </div>
    </div>
  </div>
</template>

<style scoped>
a {
  color: #42b983;
}
#Calc{
  width: 50%;
  margin: 0 auto;
}
.addRn {
  cursor: pointer;
}
.rn {
  width: 50%;
  margin: 0 auto;
  background-color: #ccc;
}
span {
  width: 45%;
  padding: 5px 0;
  display: inline-block
}
</style>
