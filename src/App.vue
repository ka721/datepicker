<template>
    <div class="inp">
      <b><lable>Введите дату:</lable></b>
      <datepicker v-bind:hArray="hArray" />
    </div>
    <div class="inp">
    <b><label>Введите событие:</label></b>    
    <div class="inp">
      <datepicker v-bind:hArray="hArray" @dateout="dateOut" :useCalendar="false"/>
      <div><input type="text" v-model="hItem.event"/></div>
      <div><button v-on:click="function() { hArray.push(Object.assign({},hItem)) }">Add</button></div>
    </div>
    </div>
</template>

<script>
import datepicker from './components/datepicker.vue'

export default {
  name: 'App',
  components: {
    datepicker
  },
  data: function() {
    return{
        ti:'',
        dateout:'',
        hItem: {date:'',event:''},
        hArray: [
        {date:'15.09.2021',event:'День школьного рюкзака'},
        {date:'15.09.2021',event:'Международный день точки'},
        {date:'25.09.2021',event:'День комиксов'},
        {date:'11.10.2021',event:'День шуршания листьями'},
        {date:'17.10.2021',event:'День электронных таблиц'}
        ]
    }
  },
  methods:{
    dateOut: function(value){
      this.hItem.date = value;
    }
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
  margin-top: 60px;
}
.inp {
        display:flex
    }
</style>
