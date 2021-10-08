<template>
  <div class="datepicker">
    <input  id="datep"
            type="text"
            v-model="datep"
            name="datep"
            placeholder="дд.мм.гггг"
            v-on:click="isVisible = true"
            v-bind:class="{ invalidinput: isInvalid }"
            ><button v-show="isVisible && useCalendar && !isInvalid" v-on:click="isVisible = false">{{upchar}}</button>
    <table class="table" v-show="isVisible && useCalendar">
      <thead>
        <tr>
          <td>
            <button class="myButtonW" v-on:click="dec">{{ toleft }}</button>
          </td>
          <td colspan="5"><center> {{monthes[month]}} {{year}} </center></td>
          <td>
              <button class="myButtonW" v-on:click="inc">{{ toright }}</button>
          </td>
        </tr>
        <tr>
          <td v-for="d in day" :key="d"><center>{{d}}</center></td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="week in calendar()" :key="week">             
          <td v-for="day in week" :key="day">
            <button class="myButtonW"
                v-show="day.index != null" v-bind:class="{ eventexist: evAr[day.index] != '' }"
                v-on:click="setDateP($event),isVisible=false" v-bind:value='day.index' :title='evAr[day.index]'> {{ day.index }} </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'datepicker',
  props: {
        hArray: {
          type: [String, String],
          requared: false
        },
        useCalendar:{
          default: true,
          requared: false,
          type: Boolean
        }
  },
  data: function()
  {
    return{
      evAr: [],
      datep: '',
      month: new Date().getMonth(),    
      year: new Date().getFullYear(), 
      day:["Пн", "Вт","Ср","Чт","Пт","Сб", "Вс"],
      monthes:["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"],
      date: new Date(),
      isVisible: false,
      isInvalid: false,
      toleft: '<',
      toright: '>',
      upchar: '⇧'
    }
  },
  watch: {
    datep(value){
      this.datep = value;
      if(this.datep.length === 10 && this.checkDate(this.datep)){
        this.isInvalid = false;
        this.$emit('dateout', this.datep);
        this.isVisible = false;
        this.month = this.datep.substring(3,5) - 1;
        this.year = this.datep.substring(6,11);
      } else {
        this.isInvalid = true;
        this.isVisible = true;
      }
    }
  },
  methods:{
    checkDate(dt) {
      var regex = /^([0-3]\d{1})\.([0-1]\d{1})\.([1-2]\d{3})$/
      if (!regex.test(dt)) return false;
      var result = dt.match(regex);
      var d = parseInt(result[1],10);
      var m = parseInt(result[2],10);
      var y = parseInt(result[3],10);
      var idays = 0;
      if (m < 1 || m > 12 || y < 1900 || y > 2100) return false;
      if (m == 2){
              idays = ((y % 4) == 0) ? 29 : 28;
      } else if(m == 4 || m == 6 || m == 9 || m == 11){
              idays = 30;
      } else{
              idays = 31;
      }
      return (d >= 1 && d <= idays);
    },
    setDateP(event) {
      var dt = event.target.value;
      this.datep = this.getDateString(dt.toString());
    },
    getDateString(daySet) {
      var monthSet = (this.month + 1).toString();
      if (daySet.length === 1) {
        daySet = "0" + daySet;
      }
      if (monthSet.length === 1) {
        monthSet = "0" + monthSet;
      }
      return daySet + "." + monthSet + "." + this.year;
    },
    getValEvent(daySet){
      var dt = this.getDateString(daySet.toString());
      var retVar = "";
      var newArray = this.hArray.filter(function(item){
        return item.date.indexOf(dt) > -1;
      })
      newArray.forEach( function(item){
        if(retVar === "") retVar = item.event;
        else retVar += "\n" + item.event;
      })
      return retVar;
    },
    calendar(){
      var days = [];
      var week = 0;
      var a;
      days[week] = [];
      var dlast = new Date(this.year, this.month + 1, 0).getDate();
      for (let i = 1; i <= dlast; i++) {

        this.evAr[i] = this.getValEvent(i);
        if (new Date(this.year, this.month, i).getDay() != 1) {
          a = {index:i};
          days[week].push(a);
        } else {
          week++;
          days[week] = [];
          a = {index:i};
          days[week].push(a);
        }
      }
      if (days[0].length > 0) {
        for (let i = days[0].length; i < 7; i++) {
          days[0].unshift('');
        }
      }
      return days;
    },
    dec(){
      this.month--;
      if (this.month < 0) {
        this.month = 11;
        this.year--;
      }
    },
    inc(){
      this.month++;
      if (this.month > 11) {
        this.month = 0;
        this.year++;
      }
    }
  },
  computed:{
    getHolidays: function() {
      return this.hArray.length;
    },
    totalItems () {
      return this.hArray.reduce((sum) => { return sum + 1 }, 0)
    },
    validate() {
      if(this.datep.length > 0) {
        return true;
      } else {
        return false;
      }
    }
  }

}
</script>

<style scoped>
button{
  height:20px;
  width:40px;
  box-shadow:inset 0px 1px 0px 0px #ffffff;
	background:linear-gradient(to bottom, #ffffff 5%, #f6f6f6 100%);
	background-color:#ffffff;
	border-radius:6px;
	border:1px solid #dcdcdc;
	display:inline-block;
	cursor:pointer;
	color:#666666;
	font-family:Arial;
	font-size:15px;
	font-weight:bold;
	padding:1px;
	text-decoration:none;
	text-shadow:0px 1px 0px #ffffff;
}

button:hover {
	background:linear-gradient(to bottom, #f6f6f6 5%, #ffffff 100%);
	background-color:#f6f6f6;
}

button:active {
	position:relative;
	top:1px;
}

.eventexist{
    background:linear-gradient(to bottom, #b0f8b0 5%, #28d645 100%);
}

.invalidinput{
    box-shadow: 3px 3px 3px red;
}

</style>
