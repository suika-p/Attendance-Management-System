<template>
<div id="app">

<nav class="navbar is-light head" role="navigation" aria-label="main navigation">
  <div class="navbar-brand">
    <a class="navbar-item">
      <img src="~/assets/icon.png" width="40" height="80">
    </a>
  </div>
  <div class="navbar-menu">
    <div class="navbar-start">
      <a class="navbar-item" href="/index">
        Attend
      </a>
      <a class="navbar-item" href="/profile">
        Member
      </a>
    </div>
  </div>
</nav>

<div class="navbar" style="z-index:-1"></div>


<div class="hero is-primary is-bold hero-set">

  <div class="hero-body">
    <div class="container has-text-centered">
      <h1 class="title">Atendence Status</h1>
      <h2 class="subtitle">Member infomation</h2>
    </div>
  </div>

  <div class="hero-foot">
    <nav class="tabs">
      <div class="container list_button">
        <ul>
          <li>
            <button v-on:click="inputOk" class="button is-primary">Add Name</button>
          </li>
          <li>
            <input v-if="isOK" v-on:keyup.enter="finOk" 
            v-model="nameInput" type="textbox" 
            class="input is-primary" placeholder="write input-name">
          </li>
          <li>
            <button v-on:click="delOk" class="button is-primary">Del Name</button>
          </li>
          <li>
            <input v-if="isdelOK" v-on:keyup.enter="delFinOk" 
            v-model="nameDel" type="textbox" 
            class="input is-primary" placeholder="write del-name">
          </li>
        </ul>
      </div>
    </nav>
  </div>
</div>

<div class="dropdown is-hoverable is-up sort-button">
  <div class="dropdown-trigger">
    <button class="button is-primary is-light" aria-haspopup="true" aria-controls="dropdown-menu3">
      <span>Sort</span>
      <span class="icon is-small">
        <i class="fas fa-angle-down" aria-hidden="true"></i>
      </span>
    </button>
  </div>
  <div class="dropdown-menu" role="menu">
    <div class="dropdown-content">
      <a href="#" class="dropdown-item" v-on:click="sortDescending">
        descending(days)
      </a>
      <a href="#" class="dropdown-item" v-on:click="sortAscending">
        ascending(days)
      </a>
      <a href="#" class="dropdown-item" v-on:click="sortName">
        lexical
      </a>
    </div>
  </div>
</div>

<div class="box">
  <div id="all_area" class="box_item">
    <table id="solid_area" class="table is-striped is-bordered">
      <thead>
        <tr>
          <th>Name</th>
          <th>Days Attended</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(name, key) in names" :key="key">
          <td>{{name.text}}</td>
          <td>{{name.num}}</td>
        </tr>
      </tbody>
    </table>
    <div id="scroll_area">
      <table class="table is-striped is-bordered">
        <thead>
          <tr>
            <th v-for="(i, keys) in calender.flat()" :key="keys">
                {{dayOnMonth[keys]}}/{{i}}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(_, key) in names.length" :key="key">
            <td v-for="(day, keys) in names[key].tdColors" :key="keys" 
            v-on:click="changeColor(key, keys)" 
            v-bind:style="{background:names[key].tdColors[keys]}">
            {{names[key].changeColors[keys]}}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

  <div class="box_col box_item">
    <div class="box">
      <div class="box_item">
        <div class="control">
          <textarea v-model="message" class="textarea is-focused" placeholder="Memo"></textarea>
        </div>
      </div>
      <div class="box_item">
        <button v-on:click="submitText" class="button is-primary is-focused">Submit</button>
      </div>
    </div>

    <div v-for="(item, key) in submitTexts" :key="key" class="box_item notification is-primary is-light text-container">
      <p class="str-bend">{{item}}
      </p>
    </div>

  </div>

</div>

</div>
</template>

<script>
export default {
  name: 'app',
  beforeRouteEnter(to, from, next) {
    next(vm => {
      vm.initialize();
      next();
    });
  },
  data () {
    return {
      daysOfYear: 0,
      names: [
        { text: 'shimakaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'umikaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'tokitsukaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'amatsukaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'inazuma', num: 0, changeColors: [], tdColors:[]},
        { text: 'ikazuti', num: 0, changeColors: [], tdColors:[]},
        { text: 'shimakaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'umikaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'tokitsukaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'amatsukaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'inazuma', num: 0, changeColors: [], tdColors:[]},
        { text: 'ikazuti', num: 0, changeColors: [], tdColors:[]},
        { text: 'shimakaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'umikaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'tokitsukaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'amatsukaze', num: 0, changeColors: [], tdColors:[]},
        { text: 'inazuma', num: 0, changeColors: [], tdColors:[]},
        { text: 'ikazuti', num: 0, changeColors: [], tdColors:[]},
      ],
      isOK: false,
      nameInput: "",
      isdelOK: false,
      nameDel: "",
      dayOnMonth: [],
      message: "",
      submitTexts: ["tokitsukaze is homecoming.", "shimakaze is sick.", "tokitsukaze is homecoming.", "shimakaze is sick."],
    }
  },

  computed: {
    calender: function() {
      var cal = new Array(12);
      var endDay = [31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
      var now = new Date();
      var y = now.getFullYear();
      if (y % 4 == 0 && y % 100 == 0) {
        endDay[1] = 28;
      }
      for (var i = 0; i < 12; i++) {
        cal[i] = new Array(endDay[i]);
        for (var j = 0; j < endDay[i]; j++) {
          cal[i][j] = j + 1;
        }
      }
      return cal
    }
  },

  methods: {
    inputOk: function() {
      this.isOK = true;
    },
    finOk: function() {
      this.names.push({text: this.nameInput, num: 0, changeColors: Array(this.daysOfYear).fill("ng"), tdColors: Array(this.daysOfYear).fill("red")});
      this.isOK = false;
      this.nameInput = "";
    },
    delOk: function() {
      this.isdelOK = true;
    },
    delFinOk: function() {
      for (var i = 0; i < this.names.length; i++) {
        if (this.names[i].text == this.nameDel) {
          this.names.splice(i, 1);
          this.nameDel = "";
          break;
        }
      }
      this.isdelOK = false;
      this.nameDel = "";
    },
    changeColor: function(x, y) {
      if (this.names[x].changeColors[y] == "ng") {
        this.names[x].changeColors[y] = "ok";
        this.names[x].tdColors[y] = "green";
        this.names[x].num++;
      } else {
        this.names[x].changeColors[y] = "ng";
        this.names[x].tdColors[y] = "red";
        this.names[x].num--;
      }
      // 色変更処理を読み込む為に行う
      for (var i = 0; i < 2;  i++) {
        this.isOK = !this.isOK;
      }
    },
    sortDescending: function() {
      this.names.sort(function(a, b){
        if (a.num > b.num) return -1;
        if (a.num < b.num) return 1;
        return 0;
      });
    },
    sortAscending: function() {
      this.names.sort(function(a, b){
        if (a.num < b.num) return -1;
        if (a.num > b.num) return 1;
        return 0;
      });
    },
    sortName: function() {
      this.names.sort(function(a, b){
        if (a.text < b.text) return -1;
        if (a.text > b.text) return 1;
        return 0;
      });
    },
    submitText: function() {
      this.submitTexts.push(this.message);
      this.message = "";
    },

    initialize: function () {

      var endDay = [31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
      var now = new Date();
      var y = now.getFullYear();
      if (y % 4 == 0 && y % 100 == 0) {
        endDay[1] = 28;
      }
      var ans = [];
      for (var i = 0; i < 12; i++) {
        this.daysOfYear += endDay[i];
        for (var j = 1; j <= endDay[i]; j++) {
          ans.push(i+1);
        }
      }
      this.dayOnMonth = ans;
      for (var i = 0; i < this.names.length; i++) {
        this.names[i].changeColors = Array(this.daysOfYear).fill("ng");
        this.names[i].tdColors = Array(this.daysOfYear).fill("red");
      }
    }
  }
}
</script>


<style>

.head {
  position: fixed;
  width: 100%;
}

/* .hero-set {

} */

#all_area {
  width: 70%;
  position: relative;
}

#solid_area {
  width: 30%;
  position: absolute;
  left: 0px;
  top: 0px;
}

#scroll_area {
  /*left: 30%;*/
  margin-left: 30%;
  top: 0;
  overflow-x: scroll;
}

/* #scroll_area table {
   width: 800px;
} */

.list_button {
  margin-left: 12%;
}

.sort-button {
  margin-left: 5%;
  margin-top: 2px;
  margin-bottom: 2px;
}

.box {
  display: flex;
  flex-direction: row;
  padding-top: 2px;
  background-color: aquamarine;
}

.box_col {
  display: flex;
  flex-direction: column;
}

.box_item {
  margin-left: 3%;
}

.text-container {
  width: 300px;
}

.str-bend {
  word-wrap: break-word;
}

.back-color {
  background-color: dodgerblue;
}

</style>

