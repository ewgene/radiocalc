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
        0: "Категория опасности 5: Опасность для человека очень маловероятна",
        1: "Категория опасности 4: Опасность для человека маловероятна",
        2: "Категория опасности 3: Опасно для человека",
        3: "Категория опасности 2: Очень опасно для человека",
        4: "Категория опасности 1: Чрезвычайно опасно для человека",
      },
      message: '',
      danger: null,
      colors: {
        0: "#009900",
        1: "#66CC00",
        2: "#FFFF00",
        3: "#FF9900",
        4: "#FF0000"
      }
    }
  },
  computed: {
    calcActivity: function () {
      for(let i=0; i<this.rnCalc.length; i++) {
        this.rnAct += this.rnCalc[i].AM/this.rnCalc[i].D_val
      }
      if(this.rnAct < 0.01) this.danger = 0
      else if(this.rnAct >= 0.01 && this.rnAct < 1) this.danger = 1
      else if(this.rnAct >= 1 && this.rnAct < 10) this.danger = 2
      else if(this.rnAct >= 10 && this.rnAct < 1000) this.danger = 3
      else if(this.rnAct >= 1000) this.danger = 4
      this.message = this.messages[this.danger]
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
            this.rnValue = null
            return
          }
/*        else {
          this.message = "Нет совпадений"
          this.rnValue = null
          return
        }*/
      }
      return this.rnCalc
    },
    displayResult() {
      console.log(this.calcActivity)
      navigator.clipboard.writeText(this.message)
      alert(this.calcActivity)
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
          <div class="status">
            <p class="message"
              :style="{color: colors[danger]}">{{this.message}}</p>
          </div>
          <button class="submit"
            @click="displayResult">
            Рассчитать категорию ЗРИ
          </button>
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
.status {
  width: 50%;
  margin: 0 auto;
}
</style>
