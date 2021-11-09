<script>
import { ref } from 'vue'


export default {
  props: [
    'radioNucList'
  ],
  data () {
    return {
      list: this.radioNucList,
      listSorted: [],
      rnCount: 0,
      rnCalc: [],
      rnValue: null,
      rnAct: 0,
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
/*  watch: {
    listSorted: {
      handler () {
        console.log(this.listSorted)
      }
    }
  },*/
  computed: {
    sortRn() {
      function compare(a, b) {
        if (a.Name_RN < b.Name_RN)
          return -1
        if (a.Name_RN > b.Name_RN)
          return 1
        return 0
      }
      this.listSorted = [...this.list]
      return this.listSorted.sort(compare)
    },
    calcActivity: function () {
      this.rnAct = 0
      for(let i=0; i<this.rnCalc.length; i++) {
        let rnMZ = this.rnCalc[i].activity/(this.rnCalc[i].D_val/1e+12)
        console.log(rnMZ)
        if(this.rnCalc[i].activity <= this.rnCalc[i].MZA) {
          continue
        }
        this.rnAct += rnMZ
      }
      if(this.rnAct < 0.01) this.danger = 0
      else if(this.rnAct >= 0.01 && this.rnAct < 1) this.danger = 1
      else if(this.rnAct >= 1 && this.rnAct < 10) this.danger = 2
      else if(this.rnAct >= 10 && this.rnAct < 1000) this.danger = 3
      else if(this.rnAct >= 1000) this.danger = 4
      this.message = this.messages[this.danger]
      console.log(this.rnAct)
      return this.rnAct
    },
  },
  methods: {
    addRn(item) {
      let rn = JSON.parse(JSON.stringify(item))
      this.rnCalc.push(rn)
      this.rnValue = null
      let leftMenu = document.getElementsByClassName("nucList")
      for(let i=0; i<leftMenu.length; i++) {
        leftMenu[i].classList.replace('off', 'on')
      }
    },
    findRn() {
      let leftMenu = document.getElementsByClassName("nucList")
      console.log(leftMenu.length)
      for(let i=0; i<leftMenu.length; i++) {
        leftMenu[i].classList.replace('on', 'off')
      }
      for(let i = 0; i<this.list.length; i++) {
        if(this.list[i].Name_RN.indexOf(this.rnValue) != -1
          || this.list[i].Name_RN_Lat.indexOf(this.rnValue) != -1
          || this.list[i].Kod_RN.indexOf(this.rnValue) != -1)
          {
            let elem = document.getElementById(this.list[i].Kod_RN)
            elem.classList.replace('off', 'on')
            console.log(elem.classList)
          }
      }
    },
    removeRn(value) {
      for(let i=0; i<this.rnCalc.length; i++) {
        if(this.rnCalc[i].Kod_RN == value) {
          this.rnCalc.splice(i, 1)
        }
      }
    },
    displayResult() {
      console.log(this.calcActivity)
      navigator.clipboard.writeText(this.message)
    },
    resetCalc() {      
      this.rnValue = null
      this.rnCalc = []
      this.rnAct = null
    }
  }
}

</script>

<template>
  <div id="Calc">
    <div class="left">
      <div class="head">
        <i class="fa fa-refresh"
        @click="resetCalc"></i>
        <input class="element"
          v-model="rnValue"
          @keyup="findRn()" />
      </div>
      <div class="list">
        <p class="nucList on"
          v-for="item in sortRn" 
            :ref="item.Kod_RN"
            :id="item.Kod_RN"
            :key="item.Kod_RN"
            @click="addRn(item)">
            <span>{{ item.Name_RN }}</span> <span>{{ item.Name_RN_Lat }}</span>
        </p>
      </div>
    </div>
    <div class="right">
      <h1>Расчёт категории ЗРИ</h1>
      <div class="rnList">
        <div v-if="this.rnCalc.length">
          <div class="rn" v-for="item in rnCalc" :key="item.id">
            <p class="nuc"> 
              <span>{{item.Name_RN_Lat}}</span> 
            </p>
            <input class="activity"
              v-model="item.activity" />
            <i class="fa fa-trash"
              @click="removeRn(item.Kod_RN)"></i>
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
  </div>
</template>

<style scoped>
a {
  color: #42b983;
}
#Calc{
  width: 100%;
  margin: 0 auto;
}

.left {
  position: absolute;
  width: 25%;
  height: 100vh;
  padding: 0 10px;
  overflow-y: scroll;
  scrollbar-width: thin;
}
.head {
  width: 24%;
  position: fixed;
  background-color: #fff;
}
.list {
  padding-top: 50px;
}
.element {
  margin-bottom: 30px;
}
.nucList {
  cursor: pointer;
  text-align: justify;
  margin: 0;
}
.nucList:after {
  content: "";
  display: inline-block;
  width: 100%;
}
.off {
  display: none;
}
.on {
  display: block;
}
.right {
  width: 75%;
  float: right;
}
.addRn {
  cursor: pointer;
}
.rn {
  width: 50%;
  margin: 10px auto;
  background-color: #ccc;
}
.fa {
  width: 21px;
  height: 21px;
  cursor: pointer;
}
.fa-trash {
  color: #f00;
}
.fa-refresh {
  color: #3c3;
}
.nuc {
  width: 45%;
  display: inline-block;
}
.activity {
  display:inline-block;
  width: 40%;
}
.status {
  width: 50%;
  margin: 0 auto;
}
</style>
